![][image1]

Faculty of Science

**Course:** CSCI 4230 Advanced Web Development

Note: The submission is due by **Sunday November 16th at 11:59 PM**.

# **Lab 8 Manual: DevOps in Action — CI/CD for a Flask App using Advanced GitHub Actions**

## **Lab Objective**

This lab focuses on establishing a robust **CI/CD pipeline** for a minimal **Flask web service** using **GitHub Actions**, integrating tools like **Codecov** for test coverage, **Dependabot** for automated dependency checks and vulnerability alerts, and **CodeQL** for deep security analysis. Furthermore, you will gain experience implementing advanced CI features such as **matrix builds** to test across multiple environments and explore options for production deployment to AWS or integrating Slack notifications.

## **Learning Objectives**

* Initialize a minimal Flask service and write a simple unit test.

* Run tests locally using pytest.

* Configure GitHub Actions for CI (test \+ coverage) and linting (Flake8).

* Integrate Codecov for coverage visualization.

* Enable Dependabot for dependency updates.

* Run CodeQL for basic security analysis.

* Use matrix builds to test across multiple Python versions.

* (Optional) Send Slack notifications and deploy to AWS.

## **Prerequisites**

* GitHub account

* Python 3.10+ installed

* Basic Flask and Git knowledge

* Optional: Slack webhook \+ AWS account (for final challenge)

## **Step-by-Step Instructions**

### **Step 1: Initializing the Flask Application and Setting Up the GitHub Repository**

**A.** Create a new GitHub repository named `flask-devops-lab`, keeping all default repository settings.

**B.** Clone the repository to your local machine using the following command (Be sure to replace **`<your-username>`** with your actual GitHub username. If you have an SSH key set up, use the appropriate SSH URL from GitHub instead):

```shell
git clone https://github.com/<your-username>/flask-devops-lab.git
cd flask-devops-lab
```

**C.** Set up a virtual environment and install all necessary dependencies:

```shell
python3 -m venv venv
source venv/bin/activate
pip install flask pytest pytest-cov
pip freeze > requirements.txt # Storing Project Dependencies
```

**D.** Add a `.gitignore` file to your project directory with the following contents:

```
venv/
__pycache__/
*.pyc
.DS_Store
```

**E.** Create app.py with the following code:

```py
from flask import Flaskapp = Flask(__name__)@app.route('/')def hello():    return 'Hello, DevOps World!'if __name__ == '__main__':    app.run()
```

### **Step 2: Add a Unit Test and Run Tests Locally to Ensure Success**

**A.** Create a file named test\_app.py and add the following code:

```py
from app import appdef test_home():    client = app.test_client()    res = client.get('/')    assert res.status_code == 200    assert b'DevOps' in res.data
```

**B.** Execute the unit test locally using the command below, and capture a screenshot confirming successful completion.

```shell
pytest -q
```

### **Step 3: Push to GitHub and Configure GitHub Actions for CI, Linting, and CodeCov**

**A.** Push your code to GitHub using the following commands:

```shell
git add .
git commit -m "Initial commit"
git branch -M main
git push -u origin main
```

**B.** Create `.github/workflows/test.yml` with the following content:

```
name: Lab 8 DevOps - Tests CI Pipeline
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.10"

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Run tests with coverage
        run: pytest --cov=app test_app.py
```

**C.** Commit and push your code to GitHub using the following commands:

```shell
git add .
git commit -m "Adding testing lint yml file"
git push -u origin main
```

**D.** Open Actions in GitHub and confirm it runs, and **capture a screenshot** confirming successful completion of the job.

**E.** Modify the test.yml workflow file to add the configuration below, enabling matrix builds for your GitHub Actions workflow.

```
name: Lab 8 DevOps - Tests CI Pipeline
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ["3.10", "3.11", "3.12"]
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Run tests with coverage
        run: pytest --cov=app test_app.py
```

**F.** Commit and push your code to GitHub using the following commands:

```shell
git add .
git commit -m "Adding matrix config to yml file"
git push -u origin main
```

**G.** Open Actions in GitHub and confirm all jobs run. **Capture a screenshot** confirming successful completion of all jobs.

**H.** Create `.github/workflows/lint.yml` with the following content:

```
name: Lab 8 DevOps - Linting Pipeline
on: [push, pull_request]
jobs:
  lint:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ["3.10", "3.11", "3.12"]
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}

      - name: Install Flake8
        run: pip install flake8

      - name: Run Flake8
        run: flake8 app.py test_app.py
```

**I.** Commit and push your code to GitHub using the following commands:

```shell
git add .
git commit -m "Adding Flake8 lint checking yml file"
git push -u origin main
```

**J.** Open Actions in GitHub and confirm all jobs run. **Capture a screenshot** confirming successful completion of all jobs.

**K.** Modify the test.yml workflow file to add the workflow (after tests) for Codecov:

```
- name: Upload coverage to Codecov
  uses: codecov/codecov-action@v2
  with:
    token: ${{ secrets.CODECOV_TOKEN }}  # Add in Repo Settings → Secrets and variables → Actions
```

**L.** Visit [https://about.codecov.io/](https://about.codecov.io/) and create a free trial account. Then navigate to **Settings → Access → Generate Token**. After giving your token a name, be sure to **save it after creation**, as it is only displayed once.

**M.** Navigate to **Settings → Secrets and variables → Actions → New repository secret** in your GitHub repository. Set the name as `CODECOV_TOKEN` and the value as your Codecov API token, then save the secret.

**N.** Commit and push your code to GitHub using the following commands:

```shell
git add .
git commit -m "Adding code coverage to test.yml file"
git push -u origin main
```

**O.** Open Actions in GitHub and confirm all jobs run. **Capture a screenshot** confirming successful completion of all jobs.

### **Step 4: Add Dependabot**

**A.** Create .github/dependabot.yml with this content:

```
version: 2updates:  - package-ecosystem: 'pip'    directory: '/'    schedule:      interval: 'weekly'
```

**B.** Commit and push your code to GitHub using the following commands:

```shell
git add .
git commit -m "Adding dependabot file"
git push -u origin main
```

**C.** In your GitHub repository, navigate to **Insights → Dependency Graph → Dependabot**. Take a screenshot of the page and briefly explore the information it provides.

### **Step 5: Add CodeQL Security Analysis**

**A.** Create .github/workflows/codeql.yml with the following:

```
name: Lab 8 DevOps - CodeQL Analysis
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  analyze:
    permissions:
      security-events: write
      actions: read
      contents: read

    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Initialize CodeQL
        uses: github/codeql-action/init@v3
        with:
          languages: python
      - name: Perform CodeQL Analysis
        uses: github/codeql-action/analyze@v3
```

**B.** Commit and push your code to GitHub using the following commands:

```shell
git add .
git commit -m "Adding codeql.yml file"
git push -u origin main
```

**C.** Open Actions in GitHub and confirm all jobs run. **Capture a screenshot** confirming successful completion of the ‘analyze’ job.

### **Step 6: (Optional) Slack Notification \+ AWS Deployment**

Slack Notification Example:

```html
- name: Notify Slack  uses: 8398a7/action-slack@v3  with:    status: ${{ job.status }}    fields: repo,message,commit,author  env:    SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```

AWS Deployment Example:

```html
- name: Deploy to AWS  run: |    pip install awscli    aws s3 sync . s3://my-flask-devops-bucket
```

## **Bonus Challenge**

Create an API route **/motivator** that returns a random motivational DevOps quote. Add a corresponding unit test to verify its functionality, and ensure that your CI pipeline passes across all configured Python versions.

## **Deliverables**

* GitHub repo with app.py and test\_app.py.  
* .github/workflows/test.yml, lint.yml and codeql.yml.  
* Dependabot and Codecov integrated.  
* Screenshots of all successful GitHub action executions.  
* (Optional) Working Slack or AWS deployment step.

[image1]: <data:image/png;base64,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>