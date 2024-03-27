Otsimo Task Github API Project

This Python scripted project is a command-line application that interacts with the Github projects API to perform a series of tasks. After authentication to Github and verifying the access code and rate limits, program asks user to choose 1 of the 3 options for tasks with "Which operation you want to proceed with?" and prompts user to answer with the number of the operation.
1-Create a New Project
2-Create a new Repository
3-Add an Issue to a Project
By these 3 options, user can create a new project, create a new repository, and add an issue to a project in a repository. This program also handles errors like if there is no such named issue in repository, rate limit is reached, access token is invalid etc. such errors lead program to close automatically. 
## Preparation
Make proper installations
1. Open terminal
2. To make sure your python is up to date
    Type: pip3 install --upgrade pip
Now download required packages
3. Type: pip install requests
4. Type: pip install gql
5. Type: pip install PyGithub


Get an Github personal access token
5. Open github.com
6. Login to your account
7. Open settings
8. Press developer settings
9. Open Personal Access Tokens
10. Choose Tokens(classic)
11. Generate new token (Token(classic))
12. Give permissions to full control of repositories (repo), update all user data and full control of projects 
12. Copy the access token to clipboard

## Download and execution
1. Press <> Code and select download ZIP, 
2. Open terminal, VSCode or Python
    a- For terminal:
        1. Go to your directory using, cd Downloads, cd otsimo-main
        2. Execute the file otsimo.py by, python3 otsimo.py
    b - For VSCode:
        1. Open the file otsimo.py and choose debugger Python Debugger then execute
    c - For Python3:
        1. Open the file otsimo.py


## Using command-line and interacting
1. Paste your access token
2. Type the number of operation you want to proceed with
3. Answer the questions that appears on command-line

Examples:
Enter your Github access token:
-access_token
Token is valid. Authenticated as buseanli.
Which operation you want to proceed with? (Type the number of the operation)
 1 Create a New Project
 2 Create a new Repository 
 3 Add an Issue to a Project 

## For option 1
-1
Enter the name for the project you want to create:
-my_project
Enter the project description:
-my_description
Project created successfully.

## For option 2
Enter the repository name that you want to add:
-2
Enter a description for the repository that you want to add:
-my_repo
Enter a description for the repository that you want to add:
-my_description
Repository 'my_repo' created successfully.

## For option 3, you should have an issue in your target project's destination repository
-3
Enter repository name that you want to add an issue:
-repo_name
Enter your project name you want to put an issue in:
-project_name
Enter the title of your issue:
-issue_name
Issue successfully added to the project.

If you have any questions or comments related, feel free to ask
My contact: elvanbuseanli@gmail.com
