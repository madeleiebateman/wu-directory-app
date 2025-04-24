# wu-directory-app
import streamlit as st
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt 
import re
st.set_page_config(page_title="WU Directory", layout="wide") 

st.title('Directory at Westminster University')
st.write("This is an enhanced alternative to the employee [directory](https://westminsteru.edu/campus-directory/index.html) at Westminster University." )

data = pd.read_csv("/Users/madeleinebateman/Desktop/WU_directory.csv")


#st.dataframe(data, hide_index=True)

department_list = np.sort(data['Department'].unique())

department_list = np.insert(department_list, 0, "All Departments")

department = st.selectbox(label="Choose one department from below:", options = department_list)

if department != "All Departments":
    data = data.query("Department == '{}'".format(department))

col1, col2, col3, col4 = st.columns([0.2,0.2,0.2,0.4])
with col1:
    st.text("Type of Role:") 
with col2:
    role_faculty = st.checkbox('Faculty', value=True)
with col3:
    role_staff = st.checkbox('Staff', value=True)


col1, col2, col3, col4 = st.columns([0.2,0.2,0.2,0.4])
with col1:
    st.text("Type of Contract:") 
with col2:
    contract_fulltime  = st.checkbox('FULL-TIME', value=True)
with col3:
    contract_parttime = st.checkbox('PART-TIME', value=True)


col1, col2, col3, col4 = st.columns([0.2,0.2,0.2,0.4])
with col1:
    st.text("Rank of Faculty:") 
with col2:
    position_assistant  = st.checkbox('Assistant', value=True)
with col3:
    position_associate  = st.checkbox('Associate', value=True)
with col4:
    position_full = st.checkbox('Full', value=True)

col1, col2, col3, col4 = st.columns([0.2,0.2,0.2,0.4])
with col1:
    st.text("Name:") 
with col2:
    regex_input = st.text_input("Type a condition:", value="")
with col4:
      apply_regex = st.checkbox("Regular Expression", value=False)



if role_faculty:
    data = data.query("Role == 'Faculty'")
if role_staff:
    data = data.query("Role == 'Staff'")

if contract_fulltime:
    data = data.query("Contract == 'FULL-TIME'")
if contract_parttime:
    data = data.query("Contract == 'PART-TIME'")

position_patterns = []
if position_assistant:
    position_patterns.append(r'Assistant')
if position_associate:
    position_patterns.append(r'Associate')
if position_full:
    position_patterns.append(r'Professor')

if position_patterns:
    regex_pattern = '|'.join(position_patterns)  # Combine patterns with OR '|'
    data = data[data['Position'].str.contains(regex_pattern, flags=re.IGNORECASE, na=False)]



if apply_regex and regex_input:
    try:
        data = data[data['Name'].str.contains(regex_input, flags=re.IGNORECASE, na=False)]
    except re.error as e:
        st.error(f"Invalid regular expression: {e}")

st.dataframe(data, hide_index=True)
