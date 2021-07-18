---
title:  "DynamoDB 데이터 보정(파이썬 활용)"
excerpt: ""

categories:
  - DynamoDB
  - python
  - boto3
last_modified_at: 2020-07-01T08:06:00-05:00
---



# delete 소스코드

- delete_item 은 PK 기준으로 삭제가 가능하다.

- 유니크한 hk 를 갖고 있는 data set 에 대해서 bulk delete 코드는 다음과 같다.


```python
log.info("==============================================================")
	log.info("DDB 벌크 삭제 with hk, sk 시작")
	log.info("==============================================================")
	log.info("")
	
	row_num = 3 // 3번째 라인부터 bulk 대상 data 
	
	for sheetName in wb.sheetnames:
		sheet = wb[sheetName]
		
		for row in range(row_num, sheet.max_row + 1):
			try :
				result = []
			
				for col in range(1, sheet.max_column + 1):
					if col in (1,2):
						result_1 = []
						if sheet.cell(row, col).value != None :
							if (sheet.cell(2, col).value) == 'N' :
								if (type(sheet.cell(2, col).value)) is float :
									result_1 = [(str(sheet.cell(1, col).value)), (float(sheet.cell(row, col).value))]
								else :
									result_1 = [(str(sheet.cell(1, col).value)), (int(sheet.cell(row, col).value))]
								result.append(result_1)
							else :
								if (sheet.cell(1, col).value) == 'hk' : // value 가 hk 이면 data row 앞에 SSHOP$ 을 concat 해주기 위함
									result_1 = [(str(sheet.cell(1, col).value)), ("SSHOP$" + str(sheet.cell(row, col).value))]
								else :
									result_1 = [(str(sheet.cell(1, col).value)), (str(sheet.cell(row, col).value))]
								result.append(result_1)
						else :
							result_1 = [(str(sheet.cell(1, col).value)), None]
							result.append(result_1)
					
				PK = result
			
				// delete_item 으로 PK 기준으로 삭제
				response = table.delete_item(	
                    Key= dict(PK)
				)

				if row_num != sheet.max_row :
					log.info("Current Excel Row Number : %s  ,   PK : %s", row_num, PK)
				elif row_num == sheet.max_row :
					log.info("Current Excel Row Number : %s  ,   PK : %s", row_num, PK)
					log.info("")
					log.info("==============================================================")
					log.info("End Excel Load Count : %s", row_num-2)
					log.info("==============================================================")
					log.info("")
                
				row_num += 1                

			except Exception as ex:
				log.error("Error Excel Row Number : %s  ,  Error PK : %s", row_num, PK)
				
				row_num += 1

	log.info("==============================================================")
	log.info("DDB 벌크 삭제 with hk, sk 종료")
	log.info("==============================================================")
```


# insert 소스코드



- 모듈 insert 소스 


```python
    ddb_table_target = dynamodb.Table(ddb_table_target_name)
 
    ddb_data_excel_document = openpyxl.load_workbook(ddb_data_file_name)
 
    ddb_data_sheet_names = ddb_data_excel_document.get_sheet_names()
 
    attr_names = []
    attr_types = []
 
    log.info("==============================================================")
    log.info("다이나모DB 모듈 데이터 bulk upload 시작")
    log.info("==============================================================")
    log.info("")
 
    for sheet_name in ddb_data_sheet_names:
 
        ddb_data_sheet = ddb_data_excel_document[sheet_name]
 
        row1 = ddb_data_sheet[1]
        row2 = ddb_data_sheet[2]
 
        cell_no = 0
 
        for cell in row1 :
            attr_names.append(cell.value)
            attr_types.append(row2[cell_no].value)
 
            cell_no += 1
 
        row_num = 3
 
        fileds_dict = {}
 
        err_attr = ""
        err_val = ""
 
 
        for i in range(row_num-1, ddb_data_sheet.max_row) :
            try:
         
                row = ddb_data_sheet[row_num]
                 
                cell_num = 0
                dcorn_no = "";
                dcorn_lnk_seq = "";
 
                for cell in row :
                     
                    err_attr = attr_names[cell_num]
                    err_val = cell.value
 
                    if cell.value != None :
                     
                        if attr_types[cell_num] == 'S' :
                                if err_attr == 'dcorn_no' :
                                    dcorn_no = str(cell.value)
                                if err_attr == 'hk' :
                                    fileds_dict.update({attr_names[cell_num] : "SSHOP$" + str(cell.value)})
                                elif err_attr == 'sk' :
                                    fileds_dict.update({attr_names[cell_num] : str(cell.value) + dcorn_no + "+" + dcorn_lnk_seq})
                                else :
                                    fileds_dict.update({attr_names[cell_num] : str(cell.value)})
                        elif attr_types[cell_num] == 'N' :
                                if err_attr == 'dcorn_lnk_seq' :
                                    dcorn_lnk_seq = str(cell.value)
                                if type(cell.value) is float :                               
                                    fileds_dict.update({attr_names[cell_num] : float(cell.value)})
                                else :
                                    fileds_dict.update({attr_names[cell_num] : int(cell.value)})
                        elif attr_types[cell_num] == 'J' :
                             
                            fileds_dict.update({attr_names[cell_num] : json.loads(cell.value, strict=False)})
 
                        else :
                            fileds_dict.update({attr_names[cell_num] : cell.value})
                     
                    else :
                        fileds_dict.update({attr_names[cell_num] : None})   
                     
                    cell_num += 1
                 
                with ddb_table_target.batch_writer() as batch:
 
                    batch.put_item(
                        Item = json.loads(json.dumps(fileds_dict), parse_float=Decimal)
                    )
         
 
                if row_num%100 == 0 :
                    log.info("엑셀 시트명 : %s    row 수 : %s", sheet_name, row_num)
                     
                elif row_num == ddb_data_sheet.max_row:
                    log.info("==============================================================")
                    log.info("엑셀 시트명 : %s    Load Count : %s", sheet_name, row_num-2)
                    log.info("==============================================================")
                    log.info("")
                 
                row_num += 1               
 
            except Exception as ex:
                log.error("bulk upload 중 에러 발생 : %s",ex)
                row_num += 1
 
    log.info("==============================================================")
    log.info("다이나모DB 모듈 bulk upload 종료")
    log.info("==============================================================")
 
 
except ProfileNotFound as ex :
    log.error("컨피덴셜 확인 요망")
 
except dynamodb_clnt.exceptions.ResourceNotFoundException as ex :
    log.error("테이블 확인 요망")
 
except Exception as ex:
    log.error(ex)
```




- 템플릿 insert 소스


```python
    ddb_table_target = dynamodb.Table(ddb_table_target_name)
 
    ddb_data_excel_document = openpyxl.load_workbook(ddb_data_file_name)
 
    ddb_data_sheet_names = ddb_data_excel_document.get_sheet_names()
 
    attr_names = []
    attr_types = []
 
    log.info("==============================================================")
    log.info("다이나모DB 템플릿 데이터 bulk upload 시작")
    log.info("==============================================================")
    log.info("")
 
    for sheet_name in ddb_data_sheet_names:
 
        ddb_data_sheet = ddb_data_excel_document[sheet_name]
 
        row1 = ddb_data_sheet[1]
        row2 = ddb_data_sheet[2]
 
        cell_no = 0
 
        for cell in row1 :
            attr_names.append(cell.value)
            attr_types.append(row2[cell_no].value)
 
            cell_no += 1
 
        row_num = 3
 
        fileds_dict = {}
 
        err_attr = ""
        err_val = ""
 
 
        for i in range(row_num-1, ddb_data_sheet.max_row) :
            try:
         
                row = ddb_data_sheet[row_num]
                 
                cell_num = 0
 
                for cell in row :
                     
                    err_attr = attr_names[cell_num]
                    err_val = cell.value
 
                    if cell.value != None :
                     
                        if attr_types[cell_num] == 'S' :
                                if err_attr == 'hk' :
                                    fileds_dict.update({attr_names[cell_num] : "SSHOP$" + str(cell.value)})
                                else :
                                    fileds_dict.update({attr_names[cell_num] : str(cell.value)})
                        elif attr_types[cell_num] == 'N' :
 
                                if type(cell.value) is float :                               
                                    fileds_dict.update({attr_names[cell_num] : float(cell.value)})
                                else :
                                    fileds_dict.update({attr_names[cell_num] : int(cell.value)})
                        elif attr_types[cell_num] == 'J' :
                             
                            fileds_dict.update({attr_names[cell_num] : json.loads(cell.value, strict=False)})
 
                        else :
                            fileds_dict.update({attr_names[cell_num] : cell.value})
                     
                    else :
                        fileds_dict.update({attr_names[cell_num] : None})   
                     
                    cell_num += 1
                 
                with ddb_table_target.batch_writer() as batch:
 
                    batch.put_item(
                        Item = json.loads(json.dumps(fileds_dict), parse_float=Decimal)
                    )
         
 
                if row_num%100 == 0 :
                    log.info("엑셀 시트명 : %s    row 수 : %s", sheet_name, row_num)
                     
                elif row_num == ddb_data_sheet.max_row:
                    log.info("==============================================================")
                    log.info("엑셀 시트명 : %s    Load Count : %s", sheet_name, row_num-2)
                    log.info("==============================================================")
                    log.info("")
                 
                row_num += 1               
 
            except Exception as ex:
                log.error("bulk upload 중 에러 발생 : %s",ex)
                row_num += 1
 
    log.info("==============================================================")
    log.info("다이나모DB 모듈 bulk upload 종료")
    log.info("==============================================================")
 
 
except ProfileNotFound as ex :
    log.error("컨피덴셜 확인 요망")
 
except dynamodb_clnt.exceptions.ResourceNotFoundException as ex :
    log.error("테이블 확인 요망")
 
except Exception as ex:
    log.error(ex)
```