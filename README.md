import requests

user_info = {
    "name": "Varun Bhaisaret",
    "regNo": "0827CI221147",
    "email": "varunbhaisare2210390@acropolis.in"
}

response = requests.post(
    "https://bfhldevapigw.healthrx.co.in/hiring/generateWebhook/PYTHON",
    json=user_info
)
response.raise_for_status()

response_data = response.json()
webhook_url = response_data["webhook"]
access_token = response_data["accessToken"]

sql_query = """
SELECT 
    E1.EMP_ID,
    E1.FIRST_NAME,
    E1.LAST_NAME,
    D.DEPARTMENT_NAME,
    COUNT(E2.EMP_ID) AS YOUNGER_EMPLOYEES_COUNT
FROM 
    EMPLOYEE E1
JOIN 
    DEPARTMENT D ON E1.DEPARTMENT = D.DEPARTMENT_ID
LEFT JOIN 
    EMPLOYEE E2 
    ON E1.DEPARTMENT = E2.DEPARTMENT
    AND E2.DOB > E1.DOB
GROUP BY 
    E1.EMP_ID, E1.FIRST_NAME, E1.LAST_NAME, D.DEPARTMENT_NAME
ORDER BY 
    E1.EMP_ID DESC;
""".strip()

headers = {
    "Authorization": access_token,
    "Content-Type": "application/json"
}

payload = {
    "finalQuery": sql_query
}

submission = requests.post(webhook_url, headers=headers, json=payload)
submission.raise_for_status()

print("Status Code:", submission.status_code)
print("Response:", submission.json()
