import streamlit as st
import pandas as pd
from datetime import datetime

st.set_page_config(page_title="HR 报表生成小助手", layout="centered")
st.title("📊 HR 报表生成小助手")
st.markdown("上传考勤薪酬 Excel，系统将自动生成关键人力数据指标。")

# 上传文件
uploaded_file = st.file_uploader("📁 上传考勤薪酬 Excel 文件", type=["xlsx"])

if uploaded_file:
    df = pd.read_excel(uploaded_file)

    # 显示原始数据
    with st.expander("🔍 查看上传的原始数据"):
        st.dataframe(df)

    # 转换日期
    df["入职日期"] = pd.to_datetime(df["入职日期"], errors="coerce")
    df["离职日期"] = pd.to_datetime(df["离职日期"], errors="coerce")

    # 当前月份
    today = datetime.today()
    current_month = today.strftime("%Y-%m")

    # 本月在职人数（离职日期为空或离职时间晚于当前）
    current_employees = df[df["离职日期"].isna() | (df["离职日期"] > today)]

    # 本月离职人数（离职时间在当月）
    df["离职月"] = df["离职日期"].dt.to_period("M").astype(str)
    leavers_this_month = df[df["离职月"] == current_month]

    # 指标计算
    in_service_count = len(current_employees)
    left_count = len(leavers_this_month)
    turnover_rate = round((left_count / (in_service_count + left_count)) * 100, 2) if in_service_count + left_count > 0 else 0
    avg_overtime = round(df["加班小时"].mean(), 1)
    avg_salary = round(df["工资"].mean(), 2)

    # Top 3 加班人员
    top_overtime = df.sort_values(by="加班小时", ascending=False).head(3)[["员工姓名", "加班小时"]]

    # 展示报告
    st.subheader("📈 人力关键指标报告")
    st.markdown(f"""
    - 👥 本月在职人数：**{in_service_count}**
    - 🔄 本月离职人数：**{left_count}** → 离职率：**{turnover_rate}%**
    - ⏱ 平均加班时长：**{avg_overtime} 小时**
    - 💰 平均工资水平：**¥ {avg_salary}**
    """)

    st.markdown("### 🔝 加班 Top3")
    st.dataframe(top_overtime, use_container_width=True)

    st.success("✅ 报表生成完毕！你可以继续上传其他文件查看结果。")
