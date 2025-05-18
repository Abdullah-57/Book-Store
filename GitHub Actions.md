# GitHub Actions Cheat Sheet for CI/CD Workflow

## 1. Introduction to GitHub Actions
- **What is GitHub Actions?**: A CI/CD platform integrated into GitHub that automates workflows like building, testing, and deploying code.
- **Analogy**: Think of GitHub Actions as a robot assistant that runs your project tasks (like testing or deploying) whenever you push code, create a pull request, or schedule a time.

## 2. Key Components of a GitHub Actions Workflow
### Workflow Definition
- **File Location**: `.github/workflows/<workflow-name>.yml`
  - Example: `.github/workflows/ci-cd.yml`
- **Structure**:
  - `name`: Workflow name (e.g., `Comprehensive CI/CD Workflow`).
  - `on`: Triggers for the workflow.
  - `jobs`: Tasks to execute (e.g., build, test, deploy).

### Triggers (`on`)
- **Push to a Branch**:
  - Syntax: `push: branches: - <branch-name>`
  - **Use**: Runs the workflow when code is pushed to the specified branch.
  - **Example**: Trigger on push to `main`:
    ```
    on:
      push:
        branches:
          - main
    ```
    - Scenario: Push a commit to `main` at 07:43 PM PKT on May 17, 2025 → Workflow runs.

- **Pull Request to a Branch**:
  - Syntax: `pull_request: branches: - <branch-name>`
  - **Use**: Runs the workflow when a pull request targets the specified branch.
  - **Example**: Trigger on PR to `main`:
    ```
    on:
      pull_request:
        branches:
          - main
    ```
    - Scenario: Open a PR to `main` → Workflow runs.

- **Scheduled Run**:
  - Syntax: `schedule: - cron: '<cron-expression>'`
  - **Use**: Runs the workflow at a scheduled time using cron syntax.
  - **Example**: Run daily at midnight UTC:
    ```
    on:
      schedule:
        - cron: '0 0 * * *'
    ```
    - Cron Explanation: `0 0 * * *` → 0th minute, 0th hour, every day, month, and day of week (midnight UTC).

### Jobs
- **Define a Job**:
  - Syntax: `jobs: <job-id>: runs-on: <os>`
  - **Use**: Specifies a task to run on a specific operating system.
  - **Examples**:
    - `build-and-test` on Ubuntu: `runs-on: ubuntu-latest`
    - `build-and-deploy` on Windows: `runs-on: windows-latest`
    - `linting` on macOS: `runs-on: macos-latest`

- **Conditional Job Execution**:
  - Syntax: `if: <condition>`
  - **Use**: Runs the job only if the condition is true.
  - **Example**: Run `deploy-production` only on push to `main`:
    ```
    deploy-production:
      runs-on: ubuntu-latest
      if: github.event_name == 'push'
    ```
    - Scenario: Push to `main` → Job runs; PR to `main` → Job skipped.

- **Dependencies Between Jobs**:
  - Syntax: `needs: [<job-id>]`
  - **Use**: Ensures a job waits for other jobs to complete.
  - **Example**: `notify-team` waits for `build-and-test` and `build-and-deploy`:
    ```
    notify-team:
      runs-on: ubuntu-latest
      needs: [build-and-test, build-and-deploy]
    ```
    - Scenario: `notify-team` runs only after both jobs succeed.

### Steps
- **Checkout Code**:
  - Syntax: `uses: actions/checkout@v2`
  - **Use**: Clones the repository code into the runner.
  - **Example**:
    ```
    - name: Checkout code
      uses: actions/checkout@v2
    ```
    - Used in all jobs to access the repository files.

- **Set Up a Runtime Environment**:
  - **Node.js**:
    - Syntax: `uses: actions/setup-node@v2 with: node-version: '<version>'`
    - **Use**: Installs Node.js on the runner.
    - **Example**: Set up Node.js 14:
      ```
      - name: Set up Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '14'
      ```
      - Used in `build-and-test` and `linting` jobs.
  - **Python**:
    - Syntax: `uses: actions/setup-python@v2 with: python-version: '<version>'`
    - **Use**: Installs Python on the runner.
    - **Example**: Set up Python 3.9:
      ```
      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: '3.9'
      ```
      - Used in `build-and-deploy` job.

- **Set Up Docker**:
  - Syntax: `uses: docker/setup-buildx-action@v2`
  - **Use**: Configures Docker for building and pushing images.
  - **Example**:
    ```
    - name: Set up Docker
      uses: docker/setup-buildx-action@v2
    ```
    - Used in `deploy-production` job.

- **Run Commands**:
  - Syntax: `run: <command>`
  - **Use**: Executes shell commands on the runner.
  - **Examples**:
    - Install Node.js dependencies: `run: npm install` (in `build-and-test`).
    - Run tests: `run: npm test` (in `build-and-test`).
    - Run linting: `run: npm run lint` (in `linting`).
    - Install Python dependencies: `run: pip install -r requirements.txt` (in `build-and-deploy`).
    - Build Docker image: `run: docker build -t my-app .` (in `deploy-production`).
    - Push Docker image: `run: docker push my-app` (in `deploy-production`).

- **Send Notifications**:
  - **Use**: Sends a notification (e.g., to Slack) using a webhook.
  - **Example**: Notify via Slack:
    ```
    - name: Send notification to Slack
      run: |
        curl -X POST -H 'Content-type: application/json' --data '{"text":"Build and Test completed!"}' $SLACK_WEBHOOK_URL
    ```
    - Used in `notify-team` job.
    - **Note**: `$SLACK_WEBHOOK_URL` should be stored as a GitHub Secret.

## 3. Common GitHub Actions Commands and Scenarios
### Job Examples
1. **Build and Test on Ubuntu**:
   - Job: `build-and-test`
   - Steps:
     - Checkout: `uses: actions/checkout@v2`
     - Setup Node.js: `uses: actions/setup-node@v2`
     - Install: `run: npm install`
     - Test: `run: npm test`
   - Scenario: Push to `main` → Runs tests to ensure code quality.

2. **Build and Deploy on Windows**:
   - Job: `build-and-deploy`
   - Steps:
     - Checkout: `uses: actions/checkout@v2`
     - Setup Python: `uses: actions/setup-python@v2`
     - Install: `run: pip install -r requirements.txt`
     - Deploy: `run: echo "Deploying to Staging Server"`
   - Scenario: PR to `main` → Deploys to staging for review.

3. **Linting on macOS**:
   - Job: `linting`
   - Steps:
     - Checkout: `uses: actions/checkout@v2`
     - Setup Node.js: `uses: actions/setup-node@v2`
     - Lint: `run: npm run lint`
   - Scenario: Scheduled run at midnight UTC → Ensures code style consistency.

4. **Deploy to Production on Ubuntu**:
   - Job: `deploy-production`
   - Condition: `if: github.event_name == 'push'`
   - Steps:
     - Checkout: `uses: actions/checkout@v2`
     - Setup Docker: `uses: docker/setup-buildx-action@v2`
     - Build: `run: docker build -t my-app .`
     - Push: `run: docker push my-app`
   - Scenario: Push to `main` → Deploys to production via Docker.

5. **Notify Team**:
   - Job: `notify-team`
   - Dependencies: `needs: [build-and-test, build-and-deploy]`
   - Steps:
     - Notify: `run: curl -X POST ... $SLACK_WEBHOOK_URL`
   - Scenario: After successful build and deploy → Sends Slack notification.

### Environment Variables and Secrets
- **Access GitHub Event Variables**:
  - Syntax: `github.event_name`
  - **Use**: Checks the event type (e.g., `push`, `pull_request`).
  - **Example**: `if: github.event_name == 'push'` (Used in `deploy-production`).

- **Use Secrets**:
  - Syntax: `$SECRET_NAME`
  - **Use**: Securely accesses sensitive data (e.g., passwords, tokens).
  - **Example**: Docker login in `deploy-production`:
    ```
    echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
    ```
    - `$DOCKER_USERNAME` and `$DOCKER_PASSWORD` are GitHub Secrets.

## 4. Tips for Exam Preparation
- **Objective Questions**: Memorize key syntax (e.g., `on: push: branches: - main`, `runs-on: ubuntu-latest`, `uses: actions/checkout@v2`).
- **Subjective Questions**: Explain workflows (e.g., “How to set up a CI/CD pipeline?” → Describe triggers, jobs, and steps from this cheat sheet).
- **Practice**: Create a simple GitHub repository, add a `.github/workflows/ci.yml` file, and test a basic workflow (e.g., run `npm test` on push).