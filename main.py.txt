#####################
# Welcome to Cursor #
#####################

'''
Step 1: Try generating with Cmd+K or Ctrl+K on a new line. Ask for CLI-based game of TicTacToe.

Step 2: Hit Cmd+L or Ctrl+L and ask the chat what the code does. 
   - Then, try running the code

Step 3: Try highlighting all the code with your mouse, then hit Cmd+k or Ctrl+K. 
   - Instruct it to change the game in some way (e.g. add colors, add a start screen, make it 4x4 instead of 3x3)

Step 4: To try out cursor on your own projects, go to the file menu (top left) and open a folder.
''''
import requests
from jira import JIRA
from atlassian import Confluence
from datetime import datetime, timedelta
import pandas as pd

# JIRA API配置
jira_url = "https://your-jira-instance.atlassian.net"
jira_username = "your_username"
jira_api_token = "your_api_token"

# Confluence API配置
confluence_url = "https://your-confluence-instance.atlassian.net"
confluence_username = "your_username"
confluence_api_token = "your_api_token"

# 连接JIRA
jira = JIRA(server=jira_url, basic_auth=(jira_username, jira_api_token))

# 连接Confluence
confluence = Confluence(
    url=confluence_url,
    username=confluence_username,
    password=confluence_api_token
)

# 获取HADON项目的所有ticket
jql_query = 'project = HADON'
issues = jira.search_issues(jql_query, maxResults=False)

# 计算每个人的ticket数量
ticket_count = {}
for issue in issues:
    assignee = issue.fields.assignee.displayName if issue.fields.assignee else "Unassigned"
    ticket_count[assignee] = ticket_count.get(assignee, 0) + 1

# 获取Confluence日历数据
space_key = "YOUR_SPACE_KEY"
calendar_page_id = "YOUR_CALENDAR_PAGE_ID"
calendar_content = confluence.get_page_by_id(calendar_page_id, expand='body.storage')['body']['storage']['value']

# 解析日历数据（这里需要根据实际日历格式进行调整）
# 假设日历数据格式为：姓名,开始日期,结束日期

# 创建一个当月的日期范围
today = datetime.now()
start_of_month = today.replace(day=1)
end_of_month = (start_of_month + timedelta(days=32)).replace(day=1) - timedelta(days=1)
date_range = pd.date_range(start=start_of_month, end=end_of_month)

# 创建一个DataFrame来存储统计信息
stats_df = pd.DataFrame(index=date_range, columns=list(ticket_count.keys()))

# 填充ticket数量
for person, count in ticket_count.items():
    stats_df[person] = count

# 填充休假信息（这里需要根据实际日历数据格式进行调整）
# 示例代码：
# for line in calendar_content.split('\n'):
#     name, start_date, end_date = line.split(',')
#     start_date = datetime.strptime(start_date, '%Y-%m-%d')
#     end_date = datetime.strptime(end_date, '%Y-%m-%d')
#     date_range = pd.date_range(start=start_date, end=end_date)
#     stats_df.loc[date_range, name] = '休假'

# 打印统计表
print(stats_df)

# 可以选择将统计表保存为CSV文件
# stats_df.to_csv('team_stats.csv')

