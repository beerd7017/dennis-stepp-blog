+++
title = "Checking HTTP Request/Response in PowerShell"
description = "A very quick look at checking HTTP requests/responses using PowerShell"
date = "2018-07-03"
categories = ['PowerShell']
tags = ['PowerShell']
thumbnail = "img/posts/uricheck/powershell.png"
+++

This is as much for my reference as it may be yours, but here is a quick PowerShell script to check a HTTP request/response.


    # Create the request
    $HTTP_Request = [System.Net.WebRequest]::Create('http://www.google.com')
    
    # Get the response
    $HTTP_Response = $HTTP_Request.GetResponse()
    
    # Get the HTTP as a interger
    $HTTP_Status = [int]$HTTP_Response.StatusCode
    
    If ($HTTP_Status -eq 200) {
        Write-Host "Site is OK!"
    }
    Else {
        Write-Host "The Site may be down, please check!"
    }
    
    # Clean up and close the request.
    $HTTP_Response.Close()
    
Thanks for reading and good luck!