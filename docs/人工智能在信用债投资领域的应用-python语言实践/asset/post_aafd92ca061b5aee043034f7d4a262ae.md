
"0.58% ","0.86% ","1.68% ","3.98% ","7.31% ","11.01% ","56.5% "]
    write_dataframe_to_excel(ws8,selection_list,start_col=4,start_row=65,index=False,columns=False)

    ws8["E64"].value='与 SP 对应的信用等级'
    selection_list=pd.DataFrame()
    selection_list["a"]=["AAA/AA-","A+, A, A-","BBB+, BBB","BBB-",
    "BB+","BB","BB-","B+","B","B-","CCC/C"]
    write_dataframe_to_excel(ws8,selection_list,start_col=5,start_row=65,index=False,columns=False)

    ws8["F64"].value='上调'
    selection_list=pd.DataFrame()
    selection_list["a"]=[0,1,2,3,4,5,6,7,8,9,-1,-2,-3,-4,-5,-6,
-7,-8]
    write_dataframe_to_excel(ws8,selection_list,start_col=6,start_row=65,index=False,columns=False)

    ws8["G64"].value='下调'
    selection_list=pd.DataFrame()
    selection_list["a"]=[-1,-2,-3,-4,-5,-6,-7,-8]
    write_dataframe_to_excel(ws8,selection_list,start_col=7,start_row=65,index=False,columns=False)

    ws8["H110"].value='内部等级'
    selection_list=pd.DataFrame()
    selection_list["a"]=["D","C","CC","CCC-","CCC","CCC+","B-",
    "B","B+","BB-","BB","BB+","BBB-","BBB","BBB+","A-","A","A+","AA-","AA","AA+","AAA"]
    write_dataframe_to_excel(ws8,selection_list,start_col=8,start_row=111,index=False,columns=False)

    ws8["I110"].value='>='
    selection_list=pd.DataFrame()
    selection_list["a"]=[0,0,0,5,10,15,20,25,30,35,40,45,50,55,60,
65,70,75,80,85,90,95]
    write_dataframe_to_excel(ws8,selection_list,start_col=9,start_row=111,index=False,columns=False)

    ws8["J110"].value='<'
    selection_list=pd.DataFrame()
    selection_list["a"]=[0,0,0,10,15,20,25,30,35,40,45,50,55,60,65,
70,75,80,85,90,95,100]
    write_dataframe_to_excel(ws8,selection_list,start_col=10,start_row=111,index=False,columns=False)

    ws8["K111"].value='实质性违约'
    ws8["K112"].value='技术性违约'

    selection_list=pd.DataFrame()
    selection_list["a"]=["Y","N"]
    write_dataframe_to_excel(ws8,selection_list,start_col=3,start_row=201,index=False,columns=False)

    selection_list=pd.DataFrame()
    selection_list["a"]=[3,2,1,0,-1,-2,-3]
    write_dataframe_to_excel(ws8,selection_list,start_col=4,start_row=201,index=False,columns=False)

    selection_list=pd.DataFrame()
    selection_list["a"]=["AAA","AA","A","BBB","BB","B","CCC",
"CC","C","D"]
    write_dataframe_to_excel(ws8,selection_list,start_col=5,start_row=201,index=False,columns=False)

    selection_list=pd.DataFrame()
    selection_list["a"]=[7,6,5,4,3,2,1,0,-1,-2,-3,-4,-5,-6,-7]
    write_dataframe_to_excel(ws8,selection_list,start_col=6,start_row=201,index=False,columns=False)

    ws8["H201"].value='内部评级标准'
    selection_list=pd.DataFrame()
    selection_list["a"]=["D","C","CC","CCC","B","BB","BBB","BBB+","A","AA","AAA"]
    write_dataframe_to_excel(ws8,selection_list,start_col=8,start_row=202,index=False,columns=False)

    ws8["I201"].value='>='
    selection_list=pd.DataFrame()
    selection_list["a"]=[0,1,2,3,4,5,6,7,8,9]
    write_dataframe_to_excel(ws8,selection_list,start_col=9,start_row=202,index=False,columns=False)

    ws8["J201"].value='<'
    selection_list=pd.DataFrame()
    selection_list["a"]=[1,2,3,4,5,6,7,8,9,10]
    write_dataframe_to_excel(ws8,selection_list,start_col=10,start_row=202,index=False,columns=False)

    ws8["L201"].value='内部等级'
    selection_list=pd.DataFrame()
    selection_list["a"]=[1,2,3,4,5,6,7,8,9,10]
    write_dataframe_to_excel(ws8,selection_list,start_col=12,start_row=202,index=False,columns=False)

    ws8["M201"].value='>='
    selection_list=pd.DataFrame()
    selection_list["a"]=[0,10,20,30,40,50,60,72,82,92]
    write_dataframe_to_excel(ws8,selection_list,start_col=13,start_row=202,index=False,columns=False)

    ws8["N201"].value='<'
    selection_list=pd.DataFrame()
    selection_list["a"]=[10,20,30,40,50,60,72,82,92,100]
    write_dataframe_to_excel(ws8,selection_list,start_col=14,start_row=202,index=False,columns=False)

    #返回生成的 Excel 版本的评分卡模型
    return wb
    #wb.save(' D:/Temp/风控模型/wbtest.xlsx')

    #使用开发的评分卡模型,评估样本公司
    def company_scoring_and_ranking(factors_set,boundarys,importance,dictionary,final_w,qualitative_data):
        output_set=pd.DataFrame()
        weight_list=importance["importance"]
        coefficient_list=importance["coefficients"]
        order_list=[]
        f_num=int((len(factors_set.columns)-3)/3)
        f_names=list(factors_set.columns)
        f_names=f_names[2:(2+f_num)]

        for i in range(len(f_names)):
            for j in range(len(dictionary["排序"])):
                if(f_names[i]==dictionary["字段"][j]):
                    order_list.append(int(dictionary["排序"][j]))
        order_list=order_list+order_list
        for i in range(len(f_names)):
            order_list.append(-1)
        factors_set.index=range(len(factors_set))
        for i in range(len(factors_set["CODE"])):
            # i=10
            score_record=pd.Series()
            score_record["CODE"]=factors_set.ix[i,"CODE"]
            score_record["COMP_NAME"]=factors_set["COMP_NAME"].iloc[i]
            isDefault=list(factors_set["isDefault"])[i]
            scores=[]
            factors=factors_set.iloc[i,2:(len(factors_set.columns)-1)]
            # 查询评分表
            for j in range(len(factors)):
                # j=1
                if factors[j] ==None:
                    scores.append(0)
                elif factors[j] <boundarys.iloc[j,0]:
                    if order_list[j] ==1 or (order_list[j] ==0 and coefficient_list[j] <0):
                        scores.append(0)
                    else:
                        scores.append(100)
                elif factors[j] >=boundarys.iloc[j,0] and factors[j] < boundarys.iloc[j,1]:
                    if order_list[j] ==1 or (order_list[j] ==0 and coefficient_list[j] <0):
                        scores.append(10)
                    else:
                        scores.append(90)
                elif factors[j] >=boundarys.iloc[j,1] and factors[j] < boundarys.iloc[j,2]:
                    if order_list[j] ==1 or (order_list[j] ==0 and coefficient_list[j] <0):
                        scores.append(20)
                    else:
                        scores.append(80)
                elif factors[j] >=boundarys.iloc[j,2] and factors[j] < boundarys.iloc[j,3]:
                    if order_list[j] ==1 or (order_list[j] == 0 and coefficient_list[j] <0):
                        scores.append(30)
                    else:
                        scores.append(70)
                elif factors[j] >=boundarys.iloc[j,3] and factors[j] < boundarys.iloc[j,4]:
                    if order_list[j] ==1 or (order_list[j] == 0 and coefficient_list[j] <0):
                        scores.append(40)
                    else:
                        scores.append(60)
                elif factors[j] >=boundarys.iloc[j,4] and factors[j] < boundarys.iloc[j,5]:
                    if order_list[j] ==1 or (order_list[j] == 0 and coefficient_list[j] <0):
                        scores.append(50)
                    else:
                        scores.append(50)
                elif factors[j] >=boundarys.iloc[j,5] and factors[j] < boundarys.iloc[j,6]:
                    if order_list[j] ==1 or (order_list[j] == 0 and coefficient_list[j] <0):
                        scores.append(60)
                    else:
                        scores.append(40)
                elif factors[j] >=boundarys.iloc[j,6] and factors[j] < boundarys.iloc[j,7]:
                    if order_list[j] ==1 or (order_list[j] == 0 and coefficient_list[j] <0):
                        scores.append(70)
                    else:
                        scores.append(30)
                elif factors[j] >=boundarys.iloc[j,7] and factors[j] < boundarys.iloc[j,8]:
                    if order_list[j] ==1 or (order_list[j] == 0 and coefficient_list[j] <0):
                        scores.append(80)
                    else:
                        scores.append(20)
                elif factors[j] >=boundarys.iloc[j,8] and factors[j] < boundarys.iloc[j,9]:
                    if order_list[j] == 1 or (order_list[j] == 0 and coefficient_list[j] <0):
                        scores.append(90)
                    else:
                        scores.append(10)
                else:
                    if order_list[j] == 1 or (order_list[j] == 0 and coefficient_list[j] <0):
                        scores.append(100)
                    else:
                        scores.append(0)
        # 计算得到定量总得分
        score_record["SCORE_QUANTITATE"]=sum(weight_list * scores)
        # 定性部分的得分
        level_list=[[100,0],
                    [100,50],
                    [100,60,30],
                    [100,75,50,25],
                    [100,80,60,40,20]]
        # 定性部分的选项
        selection_list=[['A','B'],
                        ['A','B'],
                        ['A','B','C'],
                        ['A','B','C','D'],
                        ['A','B','C','D','E']]

        level_select_list=[5,5,5,5,5,2,3,1,1,1,4]
        # 计算得到定性总得分
        factors=qualitative_data[qualitative_data['COMP_NAME'] == str(factors_set.iloc[i,1])]
            if len(factors) ==0:
                score_record["SCORE_QUALITATIVE"]=score_record["SCORE_QUANTITATE"]
            else:
                factors=factors.iloc[0][2:]
                scores_2=[]
                for j in range(len(factors)):
                    # j=1
                    level_select=level_select_list[j]
                    marking=pd.DataFrame()
                    marking["SELECTION"]=selection_list[level_select-1]
