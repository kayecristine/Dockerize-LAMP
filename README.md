# Setting Up a LAMP Stack with Docker

## Prerequisites
Ensure you have the following installed on your Windows machine:

* Docker Desktop (running in WSL2 mode)
* PowerShell

## Step 1: Create a Project Directory
Open PowerShell and navigate to the location where you want to store your project. Run:

```powershell
Copy
Edit
mkdir lamp_docker
cd lamp_docker
```

## Step 2: Create Required Files
** 2.1 Create docker-compose.yml
Run the following command to create the file:

```powershell
Copy
Edit
New-Item -Path . -Name "docker-compose.yml" -ItemType "file"
```

Then, open it in Notepad or any text editor and paste the following content:
