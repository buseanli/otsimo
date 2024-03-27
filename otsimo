import requests
from github import Github
import sys
from gql import GraphQLRequest
from gql import Client

#Github access token is used to authenticate the user via the API
access_token= input("Enter your Github access token: ")

#Verifying the authentication and program stops if authentication is not valid or is expired.
try:
    # Initialize the Github instance with your token
    g = Github(access_token)
    
    # Attempt to fetch the authenticated user's information
    user = g.get_user()
    print(f"Token is valid. Authenticated as {user.login}.")
except Exception as e:
    print("Token is invalid or expired.")
    sys.exit()

#Function to check rate limit so program stops if rate limit is already reached
def check_rate_limit(token):
    headers = {"Authorization": f"token {token}"}
    response = requests.get("https://api.github.com/rate_limit", headers=headers)
    if response.status_code == 200:
        rate_limit_info = (response.json())['resources']['core']['remaining']
        if rate_limit_info <=0:
            print("Rate limit reached.")
    else:
        print("Failed to fetch rate limit status.")
        sys.exit()

check_rate_limit(access_token)

#Function to get create Github repositories
def create_github_repo(token, repo_name, repo_description):
    headers = {
        "Authorization": f"token {token}",
        "Accept": "application/vnd.github.v3+json"
    }
    data = {
        "name": repo_name,
        "description": repo_description,
        "private": False,
    }
    response = requests.post("https://api.github.com/user/repos", headers=headers, json=data)
    if response.status_code == 201:
        print(f"Repository '{repo_name}' created successfully.")
        return response.json()
    else:
        print(f"Failed to create repository: {response.status_code}")
        print(response.json())
        sys.exit()
        
#Function to get repository IDs
def get_repository_id(token, owner, repo_name):
    endpoint = f'https://api.github.com/repos/{owner}/{repo_name}'
    headers = {
        'Authorization': 'Bearer ' + token,
    }

    response = requests.get(endpoint, headers=headers)

    if response.status_code == 200:
        data = response.json()
        repository_id = data['node_id']
        return repository_id
    else:

        print("Failed to fetch repository ID. Status code:", response.status_code)
        sys.exit()
        
#Function to get issue's node IDs
def get_issue_node_id(token, owner, repo_name, issue_number):
    endpoint = "https://api.github.com/graphql"

    query = """
    query($owner: String!, $repoName: String!, $issueNumber: Int!) {
      repository(owner: $owner, name: $repoName) {
        issue(number: $issueNumber) {
          id
        }
      }
    }
    """
    variables = {
        "owner": owner,
        "repoName": repo_name,
        "issueNumber": issue_number
    }
    
    headers = {
        "Authorization": f"Bearer {token}",
        "Content-Type": "application/json"
    }
    
    response = requests.post(endpoint, json={'query': query, 'variables': variables}, headers=headers)

    if response.status_code == 200:
        data = response.json()
        issue_id = data["data"]["repository"]["issue"]["id"]
        return issue_id
    else:
        print(f"Failed to get issue node ID. Status code: {response.status_code}, Response: {response.text}")
        sys.exit()

#Function to get projects node IDs
def get_project_node_ids(owner, repo_name, token):
    query = """
    query($owner: String!, $repoName: String!) {
      repository(owner: $owner, name: $repoName) {
        projectsV2(first: 100) {
          nodes {
            title
            id
          }
        }
      }
    }
    """
    variables = {"owner": owner, "repoName": repo_name}
    headers = {"Authorization": f"Bearer {token}", "Content-Type": "application/json"}
    response = requests.post('https://api.github.com/graphql', json={'query': query, 'variables': variables}, headers=headers)
    
     #fetching project names and node IDs in a list
    project_node_ids = []
    if response.status_code == 200:
        projects = response.json()['data']['repository']['projectsV2']['nodes']
        for project in projects:
            project_node_ids.append({"name": project['title'], "node_id": project['id']})
        return project_node_ids
    else:
        #if there exists an error it gives an error message
        print(f"Failed to fetch projects. Status code: {response.status_code}, Response: {response.text}")
        sys.exit()

#Function to add issue to a project
def add_issue_to_project(token, project_node_id, issue_node_id):
    mutation = """
    mutation($projectId: ID!, $contentId: ID!) {
      addProjectV2ItemById(input: {projectId: $projectId, contentId: $contentId}) {
        item {
          id
        }
      }
    }
    """
    variables = {
        "projectId": project_node_id,
        "contentId": issue_node_id
    }
    
    headers = {
        "Authorization": f"Bearer {token}",
        "Content-Type": "application/json"
    }
    
    response = requests.post('https://api.github.com/graphql', json={'query': mutation, 'variables': variables}, headers=headers)
    
    if response.status_code == 200:
        print("Issue successfully added to the project.")
        return response.json()
    else:
        print(f"Failed to add issue to project. Status code: {response.status_code}, Response: {response.text}")
        sys.exit()
    
#Function to get all issues in selected repository
def get_all_issues(token, owner, repo):
    issues_url = f'https://api.github.com/repos/{owner}/{repo}/issues'
    headers = {'Authorization': f'token {token}'}
    all_issues = []
    while issues_url:
        response = requests.get(issues_url, headers=headers)
        issues = response.json()
        all_issues.extend(issues)
        if 'next' in response.links:
            issues_url = response.links['next']['url']
        else:
            break
    return all_issues


#def create_project(token, project_name, repo, project_description):
    # Example: 'username/repo_name'
    owner = user.login
    url = f'https://api.github.com/repos/{owner}/{repo}/projects'

    headers = {

        "Authorization": f"token {token}",
        "Accept": "application/vnd.github+json"
    }
    path = {
        "Owner": owner,
        "Repo": repo
    }
    payload = {
        'name': project_name,
        'body': project_description
    }

    response = requests.post(url, json=payload, headers=headers)
    if response.status_code == 201:
        print(f"Project '{project_name}' created successfully.")
        return response.json()
    else:
        print(f"Failed to create project: {response.status_code}")
        print(response.json())
        sys.exit()

#project_name = input("Enter the project name: ")
#project_description = input("Enter the project description: ")
#repo_name_project= input("Enter the repository's name for project to be added in: ")


#def create_user_project(token, project_new_name,description_new ):
    
    headers = {
    "Authorization": f"token {token}",
     "Accept": "application/vnd.github+json",
    }

    url = 'https://api.github.com/user/projects'
    payload = {
    'name': project_new_name,
    'body': description_new
    }

    response = requests.post(url, json=payload, headers=headers)

    if response.status_code == 201:
        print(f"Project {project_new_name} created successfully.")
        print(response.json())
    else:
        print(f"Failed to create project: {response.status_code}")
        print(response.json())

# Function to make GraphQL requests
def graphql_request(query, variables=None, token=""):
    headers = {
        "Authorization": f"Bearer {token}",
        "Content-Type": "application/json",
    }
    payload = {"query": query, "variables": variables}
    response = requests.post('https://api.github.com/graphql', headers=headers, json=payload)
    if response.status_code == 200:
        return response.json()
    else:
        print(f"GraphQL request failed with status code {response.status_code}")
        print(response.json())
        return None

# Function to get the node ID of the authenticated user
def get_user_node_id(token):
    query = """
    query {
        viewer {
            id
        }
    }
    """
    result = graphql_request(query=query, token=token)
    if result and "data" in result and "viewer" in result["data"]:
        return result["data"]["viewer"]["id"]
    else:
        return None

# Function to create a new project
def create_project(node_id, project_name, project_description, token):
    mutation = """
    mutation ($nodeId: ID!, $name: String!, $body: String!) {
        createProject(input: {name: $name, body: $body, ownerId: $nodeId}) {
            project {
                id
                name
                body
            }
        }
    }
    """
    variables = {
        "nodeId": node_id,
        "name": project_name,
        "body": project_description,
    }
    result = graphql_request(query=mutation, variables=variables, token=token)
    print(result)
    if result and "data" in result and "createProject" in result["data"]:
        print("Project created successfully.")
        return result["data"]["createProject"]["project"]
    else:
        print("Failed to create project.")
        return None

owner = user.login
#Taking input of the desired operation 
inp= input("Which operation you want to proceed with? (Type the number of the operation)\n 1)Create a New Project\n 2)Create a new Repository \n 3)Add an Issue to a Project \n")
if inp=="1":
    #creates projects using node id and prompting user for inputs
    token = access_token
    project_name = input("Enter the project name: ")
    project_description = input("Enter the project description: ")
    user_node_id = get_user_node_id(token)
    if user_node_id:
        project = create_project(user_node_id, project_name, project_description, token)
        if project:
            print(f"Project ID: {project['id']}, Name: {project['name']}, Description: {project['body']}")
        else:
            print("Project creation failed.")
    else:
        print("Failed to retrieve user node ID.")
elif inp=="2":
    repo_name_add = input("Enter the repository name that you want to add: ")
    repo_description = input("Enter a description for the repository that you want to add: ")
    create_github_repo(access_token, repo_name_add, repo_description)
elif inp=="3":
    repo_name_issue = input("Enter repository name that you want to add an issue: ")
    issue_project_name = input("Enter your project name you want to put an issue in: ")
    issue_title = input("Enter the title of your issue: ")
    all_issues = get_all_issues(access_token, owner, repo_name_issue)

    for i in range(len(all_issues)):
        if all_issues[i]['title'] == issue_title:
            issue_number = all_issues[i]['number']
            break
    try:
        issue_id = get_issue_node_id(access_token, owner, repo_name_issue, issue_number)
    except Exception as e:
        print("Couldn't get issues' node IDs" ,e)
        sys.exit()

    repository_id = get_repository_id(access_token, owner, repo_name_issue)

    projects = get_project_node_ids(owner, repo_name_issue, access_token)

    for i in range(len(projects)):
        if projects[i]['name'] == issue_project_name:
            project_node_id = get_project_node_ids(owner, repo_name_issue, access_token)[i]['node_id']
            break

    result = add_issue_to_project(access_token, project_node_id, issue_id)

else:
    print("Invalid input.")
    sys.exit()
    
