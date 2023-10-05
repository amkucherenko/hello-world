# hello-world
My first repository. Yay!

Hi everyone!

I've started using GitHub as a part of Coursera Data Science Specialization course. I'm a geneticist and am currently learning R programming.



import requests
from msal import ConfidentialClientApplication
import os

# Constants for SharePoint and Graph API
TENANT_ID = 'YOUR_TENANT_ID'
CLIENT_ID = 'YOUR_CLIENT_ID'
CLIENT_SECRET = 'YOUR_CLIENT_SECRET'
SITE_ID = 'YOUR_SITE_ID'
DRIVE_ID = 'YOUR_DRIVE_ID'
FOLDER_PATH = '/sites/{}/drives/{}/root:/Folder/Subfolder'.format(SITE_ID, DRIVE_ID)
FILE_NAME = 'my_file.xlsx'

# Get Access Token using MSAL library
authority = 'https://login.microsoftonline.com/' + TENANT_ID
app = ConfidentialClientApplication(CLIENT_ID, authority=authority, client_credential=CLIENT_SECRET)

token_response = app.acquire_token_for_client(scopes=["https://graph.microsoft.com/.default"])

# Access Token for making Graph API requests
access_token = token_response['access_token']

# Graph API endpoint for file operations
url = f'https://graph.microsoft.com/v1.0{FOLDER_PATH}/{FILE_NAME}'

# Delete existing file
delete_response = requests.delete(url, headers={'Authorization': 'Bearer ' + access_token})

if delete_response.status_code == 204:
    print(f'File "{FILE_NAME}" deleted successfully.')
else:
    print(f'Failed to delete file. Status code: {delete_response.status_code}')

# Copy file from local disk to SharePoint
local_file_path = 'path/to/local/my_file.xlsx'

if os.path.exists(local_file_path):
    with open(local_file_path, 'rb') as file:
        upload_response = requests.put(url, headers={'Authorization': 'Bearer ' + access_token}, data=file)
        if upload_response.status_code == 201:
            print(f'File "{FILE_NAME}" uploaded successfully.')
        else:
            print(f'Failed to upload file. Status code: {upload_response.status_code}')
else:
    print(f'Local file "{local_file_path}" not found.')
