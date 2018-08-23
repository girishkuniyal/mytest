# mytest
 
#### Note: this code is mainly writtern in jupyter notebook. 
```python

# coding: utf-8


import re
import pandas as pd
pd.set_option('display.max_colwidth', -1)



#PREPROCESSING
#1)Copy paste only csv data from pastebin to notepad and save it as csv [data.csv].
#2)Open data.csv file in excel convert body field as string field and delete columns with
#  empty fields then save file as csv format[cleandata.csv].
#3)Open that cleandata.csv file in notepad and save it as cleandata-utf8.csv as utf-8 format.



df = pd.read_csv("cleandata-utf8.csv")
df = df.drop(['date','smstype','smsflag'],axis=1)
df.head()




df['body'].head()



df['number'].unique()




#by examining all unique message title we found that every sms number column contain bank name
#like SBI in DZ-CBSSBI,TM-SBIINB etc.
#from here we extract all common bank code name in number column carefully
#Main bank code pattern : YESBNK,BOIIND,SBI,AXISBK,HDFCBK,ICICIB,PNB

#Note: look at that NaN number field carefully. It may contain some transaction details




pd.options.display.max_rows
pd.set_option('display.max_colwidth', -1)
df[df['number'].isna()]
#dont worry this is just a remainder of some pay due




#Collect All possible different templates for best accura
#Other problem
#Repeated messages
#all message not show card or account information ,so this information is incomplete.
#Distorted messages like Chq 012740 for INR 1Ã¯Â¼Å’746




df['Credit/Debit'] = None
df['Available_balance'] = None
df['Bank']= None
df.head()




total_msg = df.shape[0]
print("Total messages:",total_msg)

#regular expression for debit 

debit1=r'.*Transfer of.*?\.(\d+).*Fees \(Incl of ST\): Rs.(\d+)'
debit2=r'.*Thank you for using your SBI Debit Card .*for a purchase worth Rs(\d+).(\d+) on POS.*'
debit3=r'.*Dear Customer, break up of consolidated charges for.*: Monthly Service Fee: Rs (\d+).*'
debit4=r'.*Rs (\d+) withdrawn from A/c .* on .* at .* .Avl bal Rs (\d+).(\d+).*'
debit5=r'.*Rs (\d+) withdrawn at .*from A/c .* on .*\.Avl bal Rs (\d+).(\d+).*'
debit6=r'.*Dear Customer, break up of consolidated charges for .*for a/c no. .*: ~Value Added SMS Alert Fee: Rs (\d+) ~Total: Rs (\d+) \(excluding GST\).*'
debit7=r'.*Your A/C .*has a debit by transfer of Rs (\d+).(\d+) on.*Avl Bal Rs (\d+).(\d+).*'
debit8=r'.*Your AC .*Debited INR (\d+).(\d+) on .*Avl Bal INR (.*)\.Plz download Buddy.*'
debit9=r'.*Thank you for using your SBI Debit Card .*for a purchase worth Rs(\d+) on POS.*'
debit10=r'.*OTP is .*for txn of INR (\d+).(\d+) at .*on card ending .*'
debit11=r'.*Rs.(\d+).(\d+) was spent on ur HDFCBank CREDIT Card ending.* on .*at .*Avl bal - Rs.(\d+).(\d+).*'

#regular expression for credit
credit1=r'.*Your AC .*Credited INR (\d+).(\d+) on.*Avl Bal INR (\d+).(\d+).*'
credit2=r'.*Dear HDFCBank cardmember, Payment of Rs (\d+) was credited to your card ending.*'
credit3=r'.*DEAR CARDMEMBER, PAYMENT OF Rs. (\d+).(\d+) RECEIVED TOWARDS YOUR CREDIT CARD ENDING .*THROUGH.*YOUR AVAILABLE LIMIT IS RS.*'
credit4=r'.*Your a/c no.*is credited by Rs.(\d+).(\d+) on .*by a/c linked to mobile.*'
credit5=r'.*Your A/C .*Credited INR (.*) on .*Deposit of Cash at .*ATM. A/c Balance INR (.*)'

#balance
bal1=r'.*Avail Bal in A/c .*: Rs. (\d+).(\d+) CR.*'



for i in range(total_msg):
    text = str(df.iloc[i][1])
    match1 = re.match(debit1,text)
    match2 = re.match(debit2,text)
    match3 = re.match(debit3,text)
    match4 = re.match(debit4,text)
    match5 = re.match(debit5,text)
    match6 = re.match(debit6,text)
    match7 = re.match(debit7,text)
    match8 = re.match(debit8,text)
    match9 = re.match(debit9,text)
    match10 = re.match(debit10,text)
    match11 = re.match(debit11,text)
    match12 = re.match(credit1,text)
    match13 = re.match(credit2,text)
    match14 = re.match(credit3,text)
    match15 = re.match(credit4,text)
    match16 = re.match(credit5,text)
    match17 = re.match(bal1,text)


    if match1:
        debit = float(match1.group(1))+float(match1.group(2))
        df.loc[i,'Credit/Debit']=-debit
        print(i)
    elif match2:
        debit=float(match2.group(1))+float('.'+match2.group(2))
        df.loc[i,'Credit/Debit']=-debit
    elif match3:
        debit=float(match3.group(1))
        df.loc[i,'Credit/Debit']=-debit
    elif match4:
        debit=float(match4.group(1))
        bal = float(match4.group(2))+float('.'+match4.group(3))
        df.loc[i,'Credit/Debit']=-debit
        df.loc[i,'Available_balance']=bal
    elif match5:
        debit=float(match5.group(1))
        bal = float(match5.group(2))+float('.'+match5.group(3))
        df.loc[i,'Credit/Debit']=-debit
        df.loc[i,'Available_balance']=bal
    elif match6:
        debit=float(match6.group(2))
        df.loc[i,'Credit/Debit']=-debit
    elif match7:
        debit=float(match7.group(1))+float('.'+match7.group(2))
        bal = float(match7.group(3))+float('.'+match7.group(4))
        df.loc[i,'Credit/Debit']=-debit
        df.loc[i,'Available_balance']=bal
    elif match8:
        debit=float(match8.group(1))+float('.'+match8.group(2))
        bal = (float(re.sub(',','',match8.group(3))))
        df.loc[i,'Credit/Debit']=-debit
        df.loc[i,'Available_balance']=bal
    elif match9:
        debit=float(match9.group(1))
        df.loc[i,'Credit/Debit']=-debit
    elif match10:
        debit=float(match10.group(1))+float('.'+match10.group(2))
        df.loc[i,'Credit/Debit']=-debit
    elif match11:
        debit=float(match11.group(1))+float('.'+match11.group(2))
        bal = float(match11.group(3))+float('.'+match11.group(4))
        df.loc[i,'Credit/Debit']=-debit
        df.loc[i,'Available_balance']=bal

    elif match12:
        credit=float(match12.group(1))+float('.'+match12.group(2))
        bal = float(match12.group(3))+float('.'+match12.group(4))
        df.loc[i,'Credit/Debit']=credit
        df.loc[i,'Available_balance']=bal
    elif match13:
        credit = float(match13.group(1))
        df.loc[i,'Credit/Debit']=credit
        df.loc[i,'Available_balance']=bal
    elif match14:
        credit=float(match14.group(1))+float('.'+match14.group(2))
        df.loc[i,'Credit/Debit']=credit
    elif match15:
        credit=float(match15.group(1))+float('.'+match15.group(2))
        df.loc[i,'Credit/Debit']=credit
    elif match16:
        credit = (float(re.sub(',','',match16.group(1))))
        bal = (float(re.sub(',','',match16.group(2))))
        df.loc[i,'Credit/Debit']=credit
        df.loc[i,'Available_balance']=bal
    elif match17:
        bal = float(match17.group(1))+float('.'+match17.group(2))
        df.loc[i,'Available_balance']=bal
    else:
        df.loc[i,'Credit/Debit']=None
        df.loc[i,'Available_balance']=None
        
    message = str(df.iloc[i][2])
    
    match_bank1=re.match(r'.*SBI.*',message)
    match_bank2=re.match(r'.*ICICI.*',message)
    match_bank3=re.match(r'.*HDFC.*',message)
    match_bank4=re.match(r'.*PNB.*',message)
    match_bank5=re.match(r'.*BOI.*',message)
    match_bank6=re.match(r'.*YESBNK.*',message)
    match_bank7=re.match(r'.*AXIS.*',message)
    
    if(match_bank1):
        df.loc[i,'Bank']='SBI'
    elif(match_bank2):
        df.loc[i,'Bank']='ICICI'
    elif(match_bank3):
        df.loc[i,'Bank']='HDFC'
    elif(match_bank4):
        df.loc[i,'Bank']='PNB'
    elif(match_bank5):
        df.loc[i,'Bank']='BOI'
    elif(match_bank6):
        df.loc[i,'Bank']='YESBNK'
    elif(match_bank7):
        df.loc[i,'Bank']='AXIS'





df




# Total amount of transaction (debit + credit) for different types of accounts 
df2 = df.copy()
df2['Credit/Debit'].replace('None', 0, inplace=True)
df2['Credit/Debit'] = df2['Credit/Debit'].abs()
df2.groupby(['user_id','Bank'])['Credit/Debit'].sum()




# Account Balance for different account best recognise by message with date.
# Here Date of message are encoded so little problem in arrange message according to date.
# Some account not have their Aval Balance message .


```
