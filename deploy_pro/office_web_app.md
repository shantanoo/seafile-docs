# Office Web app

In Seafile Professional Server Version 4.4.0 (or above), you can use Microsoft Office Web App to preview documents online. Office Web App provides the best preview for all Office format files. For organizations with Microsoft Office Volume License, it's free to use Office Web App. For more information about Office Web App and how to deploy it, please refer to https://technet.microsoft.com/en-us/library/jj219456.aspx .

Seafile's own Office file preview is still the default. To use Office Web App for preview, please add following config option to seahub_settings.py.

``` python
# Enable Office Web App
ENABLE_OFFICE_WEB_APP = True

# Url of Office Web App's discovery page
# The discovery page tells Seafile how to interact with Office Web App when view file online
# You should change `http://example.office-web-app.com` to your actual Office Web App server address
OFFICE_WEB_APP_BASE_URL = 'http://example.office-web-app.com/hosting/discovery'

# Expiration of WOPI access token
# WOPI access token is a string used by Seafile to determine the file's
# identity and permissions when use Office Web App view it online
# And for security reason, this token should expire after a set time period
WOPI_ACCESS_TOKEN_EXPIRATION = 30 * 60 # seconds

# List of file formats that you want to view through Office Web App
# You can change this value according to your preferences
# And of course you should make sure your Office Web App supports to preview
# the files with the specified extensions
OFFICE_WEB_APP_FILE_EXTENSION = ('ods', 'xls', 'xlsb', 'xlsm', 'xlsx','ppsx', 'ppt',
    'pptm', 'pptx', 'doc', 'docm', 'docx')

# Enable edit files through Office Web App
ENABLE_OFFICE_WEB_APP_EDIT = True

# types of files should be editable through Office Web App
# Note, Office Web App 2016 is needed for editing docx
OFFICE_WEB_APP_EDIT_FILE_EXTENSION = ('xlsx', 'pptx', 'docx')

```

Then restart

```
./seafile.sh restart
./seahub.sh restart
```

After you click the document you specified in seahub_settings.py, you will see the new preview page.

![office-web-app](../images/office-web-app.png)

## Trouble shooting

Understanding how the web app integration works is going to help you debugging the problem. When a user visits a file page:

1. (seahub->browser) Seahub will generate a page containing an iframe and send it to the browser
2. (browser->office web app) With the iframe, the browser will try to load the file preview page from the office web app
3. (office web app->seahub) Office web app receives the request and sends a request to Seahub to get the file content
4. (office web app->browser) Office web app sends the file preview page to the browser.

Please check the Nginx log for Seahub (for step 3) and Office Web App to see which step is wrong.
