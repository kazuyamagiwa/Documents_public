[Setting Up GitLab CI/CD Pipelines]
Setting up CI/CD in GitLab is a straightforward process that relies on a YAML configuration file stored in your repository. Here is a step-by-step guide to get your first pipeline up and running.

Step 1: Ensure You Have a GitLab Runner

GitLab CI/CD requires a Runner to execute the jobs defined in your pipeline.

- GitLab Shared Runners: If you are using GitLab.com, shared runners are usually enabled by default and free-tier hours are provided. You can check this in your project under Settings > CI/CD > Run[...]
- Self-Hosted Runners: If you are using a self-hosted GitLab instance or need a custom environment, you must install and register a GitLab Runner on your own server or infrastructure.

Step 2: Create the .gitlab-ci.yml File

The entire CI/CD process is controlled by a file named `.gitlab-ci.yml located in the root directory of your repository.

1. Go to your project repository in GitLab.
2. Click + (Create new file) or use your local IDE.
3. Name the file .gitlab-ci.yml.
4. Add a basic configuration. Here is a simple example that runs a test script:

stages:

  - build

  - test

  

build_job:

  stage: build

  script:

    - echo "Building the project..."

    - npm install

  


test_job:

  stage: test

  script:

    - echo "Running tests..."

    - npm test

Step 3: Commit and Push the File

Once you commit and push the .gitlab-ci.yml file to your default branch (e.g., main or master), GitLab will automatically detect the file and trigger your very first pipeline.

Step 4: Monitor Your Pipeline

To view the progress and results of your CI/CD pipeline:

1. In the left sidebar of your project, navigate to Build > Pipelines.
2. Click on the status icon (e.g., running, passed, or failed) of your latest pipeline to view individual stages and jobs.
3. Click on specific jobs (like build_job or test_job) to see the detailed console log and output.

Best Practices & Next Steps

- Use Environment Variables: Never hardcode sensitive data like API keys or passwords. Store them securely under Settings > CI/CD > Variables and access them in your YAML using $VARIABLE_NAME.
- Caching: Speed up your builds by caching dependencies (e.g., node_modules/ or vendor/) between jobs using the cache keyword.
- Include Templates: GitLab offers built-in CI/CD templates for popular languages and frameworks. When creating the .gitlab-ci.yml file in the GitLab UI, you can select a template to get started i[...]

To install and register a GitLab Runner on Ubuntu, follow these step-by-step instructions.

Step 1: Install GitLab Runner

Open your terminal on your Ubuntu machine and run the following commands to add the official GitLab repository and install the runner package:

1. Download and add the official GitLab repository script:curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | sudo bash
2.   
3. Install the GitLab Runner package:sudo apt-get install gitlab-runner
4.   

Once installed, the GitLab Runner service will automatically start running in the background. You can check its status using:

sudo gitlab-runner status

Step 2: Get your Runner URL and Token

Before registering, you need a token from your self-hosted GitLab instance:

1. Log into your self-hosted GitLab web interface.
2. Navigate to where you want to create the runner:

- Instance-wide (Admin): Go to Admin Area > Overview > Runners.
- Group-level: Go to your Group > Settings > CI/CD > Runners.
- Project-level: Go to your Project > Settings > CI/CD > Runners.

4. Click New instance runner (or project/group runner).
5. Give it tags if necessary, click Create runner, and you will be provided with a GitLab URL and a Registration Token (or authentication token depending on your GitLab version). Keep this handy.

Step 3: Register the Runner

Back in your Ubuntu terminal, execute the interactive registration command:

sudo gitlab-runner register

Follow the interactive prompts in the terminal:

1. Enter your GitLab instance URL (the coordinator URL, e.g., [https://gitlab.yourdomain.com](https://gitlab.yourdomain.com)):Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com )[...]
2. https://your-gitlab-instance.com
3.   
4. Enter the token you obtained from GitLab in Step 2:Please enter the gitlab-ci token for this runner:
5. glrt-xxxxxxxxxxxxxxxxxxxx
6.   
7. Enter a description for the runner (optional, you can just press Enter to use your machine's hostname):Please enter the gitlab-ci description for this runner:
8. my-ubuntu-runner
9.   
10. Enter tags associated with the runner (comma-separated, optional). These tags help target specific jobs to this runner:Please enter the gitlab-ci tags for this runner (comma separated):
11. ubuntu,docker,linux
12.   
13. Enter the executor. For most modern workflows窶覇specially if you want isolated builds using containers窶輩ou should choose docker. (Note: If you choose docker, ensure Docker is already i[...]
14. docker
15.   
16. Enter the default Docker image to be used if projects don't specify one in their .gitlab-ci.yml file:Please enter the default Docker image (e.g. ruby:3.0):
17. ubuntu:latest
18.   

If registration is successful, you will see a message like: Runner registered successfully. Feel free to start it.

Step 4: Verify the Runner

1. Return to your GitLab UI under the Runners section where you grabbed the token.
2. Refresh the page. You should now see your newly registered Ubuntu runner listed as active and online (indicated by a green dot).


To set up and run CI/CD for a Python project, you can configure your runner to handle Python environments using either the Docker executor (recommended) or the Shell executor.

Here is a complete mock installation, registration, and pipeline configuration example specifically tailored for a Python project on Ubuntu.

Step 1: Mock Registration for a Python Runner

When you run sudo gitlab-runner register, you provide specific inputs tailored for Python workloads. Here is a mock example of an interactive registration session:

$ sudo gitlab-runner register

  

Runtime platform                                    arch=amd64 os=ubuntu pid=14232 revision=1b5f13d6 version=16.8.0

(1/4) Entering the GitLab instance URL

Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com ):

https://gitlab.mycompany.local

(2/4) Entering the authentication token

Please enter the gitlab-ci token for this runner:

glrt-AbCdEf1234567890xyz

(3/4) Entering the description

Please enter the gitlab-ci description for this runner:

[ubuntu-server]: python-worker-01

(4/4) Entering the tags

Please enter the gitlab-ci tags for this runner (comma separated):

python,ubuntu,pytest

(5/4) Entering optional maintenance note

Please enter the optional maintenance note:

Dedicated runner for Python 3.11 microservices

(6/4) Entering the executor

Please enter the executor: docker, shell, kubernetes, ssh:

docker

(7/4) Entering the default Docker image

Please enter the default Docker image (e.g. ruby:3.0):

python:3.11-slim

Step 2: Create the Python Project .gitlab-ci.yml

In your Python project repository, create a .gitlab-ci.yml file in the root directory. This pipeline targets the runner we just registered using the tags parameter, installs your dependencies, an[...]

stages:

  - lint

  - test

  - package

  

variables:

  PIP_CACHE_DIR: "$CI_PROJECT_DIR/.cache/pip"

  

# Cache pip packages between builds to speed up execution

cache:

  paths:

    - .cache/pip

    - venv/

  

before_script:

  - python -V                # Print out python version for debugging

  - python -m venv venv

  - source venv/bin/activate

  - pip install --upgrade pip

  

lint_job:

  stage: lint

  tags:

    - python

  script:

    - pip install flake8

    - flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics

  


test_job:

  stage: test

  tags:

    - python

  script:

    - pip install -r requirements.txt

    - pip install pytest

    - pytest --maxfail=1 --disable-warnings -v

  


package_job:

  stage: package

  tags:

    - python

  script:

    - python setup.py sdist bdist_wheel

  artifacts:

    paths:

      - dist/*.tar.gz

      - dist/*.whl

    expire_in: 30 days

Step 3: What Happens During Execution

1. Tag Matching: When you push code, GitLab looks for an online runner tagged with python. It routes the job to your python-worker-01 runner.
2. Container Spin-up: Since you chose the docker executor with python:3.11-slim as the default image, the runner automatically spins up a clean isolated Docker container running Python 3.11.
3. Caching: The .cache/pip folder saves your downloaded packages, preventing the runner from re-downloading dependencies on every single commit.

If you are using uv (Astral's lightning-fast Python package and project manager), your GitLab CI/CD pipeline becomes much faster and cleaner. You don't need to manually create virtual environment[...]

The official ghcr.io/astral-sh/uv Docker images can be used directly, pre-packaged with Python 3.11.

Updated .gitlab-ci.yml for Python 3.11 with uv

variables:

  # Specify versions and configuration optimized for uv

  UV_VERSION: "0.5"

  PYTHON_VERSION: "3.11"

  BASE_LAYER: "bookworm-slim"

  # GitLab CI creates a separate mountpoint for the build directory,

  # so copying is required instead of hard links for the cache

  UV_LINK_MODE: "copy"

  UV_CACHE_DIR: "$CI_PROJECT_DIR/.uv-cache"

  

stages:

  - lint

  - test

  - package

  

# Cache dependencies between pipelines based on uv.lock

cache:

  - key:

    files:

      - uv.lock

    paths:

      - $UV_CACHE_DIR

  


default:

  # Official astral-sh image containing both uv and Python 3.11

  image: ghcr.io/astral-sh/uv:$UV_VERSION-python$PYTHON_VERSION-$BASE_LAYER

  

lint_job:

  stage: lint

  tags:

    - python

  script:

    - uv run ruff check .

  


test_job:

  stage: test

  tags:

    - python

  script:

    # Syncs dependencies from uv.lock into a local .venv environment automatically

    - uv sync --frozen

    - uv run pytest --maxfail=1 --disable-warnings -v

  


package_job:

  stage: package

  tags:

    - python

  script:

    - uv build

  artifacts:

    paths:

      - dist/*.tar.gz

      - dist/*.whl

    expire_in: 30 days

  


# Clean up cache size post-build

after_script:

  - uv cache prune --ci

Key Changes When Using uv:

1. Dedicated Docker Image: Instead of pulling a generic python image and installing tools manually, the runner uses ghcr.io/astral-sh/uv:0.5-python3.11-bookworm-slim, which has uv and Python 3.11[...]

2. uv sync --frozen: This command installs your project dependencies cleanly and instantly using the lockfile (uv.lock), ensuring your CI environment matches your local development environment pr[...]

3. uv run: This automatically executes commands (like pytest or linters) inside the project's virtual environment without requiring manual activation steps (source .venv/bin/activate).

4. Optimized Caching: Caching the $UV_CACHE_DIR combined with uv cache prune --ci keeps pipeline execution rapid while preventing storage bloat on your self-hosted server.

Yes, exactly! What I showed you above is using a uv-based workflow on your self-hosted GitLab server.

To clarify how it works: you don't actually need a special "uv-only" runner. Because your GitLab runner uses the docker executor, it can dynamically spin up any container image for each job. When[...]

If your local development environment uses uv, setting up your GitLab CI/CD this way is the best approach because it guarantees your pipeline environment mirrors your local setup almost identical[...]

How to Match Your Local uv Environment to GitLab

If you use uv locally, you likely have a pyproject.toml and a uv.lock file in your project root. Here is how your workflow syncs between local and GitLab:

1. Locally: You run uv sync to install dependencies and uv run pytest to run tests.
2. On GitLab: The pipeline uses uv sync --frozen and uv run pytest inside the Docker container, reading that exact same uv.lock file to guarantee identical package versions.

Summary of What You Need on Your Ubuntu Server:

- Docker installed: Since your runner is configured with the docker executor, your Ubuntu server just needs Docker running so it can pull the uv images. No manual installation of uv or Python is [...]

To install Docker on Ubuntu (for your self-hosted GitLab runner), follow these standard installation steps using Docker's official apt repository.

Step 1: Remove Old Versions

If you have any old versions of Docker installed, remove them first to avoid conflicts:

sudo apt-get remove docker docker-engine docker.io containerd runc

Step 2: Set Up Docker's Apt Repository

1. Update your package index and install packages to allow apt to use a repository over HTTPS:sudo apt-get update
2. sudo apt-get install ca-certificates curl
3.   
4. Add Docker窶冱 official GPG key:sudo install -m 0755 -d /etc/apt/keyrings
5. sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
6. sudo chmod a+r /etc/apt/keyrings/docker.asc
7.   
8. Set up the stable repository by running the following command:echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
9.   
10. Update the package index again:sudo apt-get update

Step 3: Install Docker Engine

Install the latest version of Docker and associated plugins:

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

Step 4: Verify Docker is Running

Once installed, the Docker service starts automatically. Verify that it is running correctly:

sudo systemctl status docker

To test that everything works without errors, run the hello-world container:

sudo docker run hello-world

Step 5: (Optional) Run Docker Without sudo

By default, executing docker commands requires sudo. If you want your GitLab runner user (or your current user) to run Docker commands seamlessly, add your user to the docker group:

sudo usermod -aG docker $USER

Note: If you run this, log out and log back in (or restart your terminal/server) for the group changes to take effect.

Yes, sudo apt update and sudo apt install will work, but if your updates can't be found, it usually means the official GitLab repository script wasn't run properly or your Ubuntu codename/archite[...]

Because you installed GitLab on this Ubuntu machine, your package manager (apt) relies on repository configuration files placed in /etc/sources.list.d/. If apt update doesn't find GitLab or GitLa[...]

Step 1: Check if the Repository Files Exist

Run this command to see if GitLab repositories are registered with apt:

ls /etc/apt/sources.list.d/

You should see files like gitlab_gitlab-ce.list (or ee) and/or runner_gitlab-runner.list.

- If the files are missing: You need to re-run the official setup script provided by GitLab to hook into apt. For GitLab Runner, that is:curl -L "https://packages.gitlab.com/install/repositories/[...]
-   
    (If you are trying to update the main GitLab application itself instead of the runner, use the gitlab-ce or gitlab-ee script URL instead).

Step 2: Fix "Unable to Locate Package" or GPG Key Errors

If the list file exists, but sudo apt update throws errors (such as missing GPG public keys or "Unable to locate package"), it's usually because the repository key expired or failed to sync.

You can manually force-refresh the repository configuration and keys for GitLab Runner by running:

curl -fsSL "https://packages.gitlab.com/runner/gitlab-runner/gpgkey" | sudo gpg --dearmor -o /usr/share/keyrings/runner_gitlab-runner-archive-keyring.gpg

sudo apt update

If you are looking to fix the main GitLab server package repository instead, refresh its key with:

curl -fsSL "https://packages.gitlab.com/gpg.key" | sudo gpg --dearmor -o /usr/share/keyrings/gitlab-archive-keyring.gpg

sudo apt update

Step 3: Check Your Ubuntu Codename

Sometimes, if you upgraded your Ubuntu OS version (e.g., from focal to jammy), the hardcoded codename inside the GitLab repository file might still point to the old version.

Open the repository file to check:

cat /etc/apt/sources.list.d/runner_gitlab-runner.list

It should dynamically match your current Ubuntu release name (like jammy or noble). If it has an old codename, re-running the installation script from Step 1 will safely rewrite it to match your [...]

That is completely understandable. Running a self-hosted GitLab instance comes with maintenance overhead, and keeping it stable while teams depend on it daily is a high-stakes responsibility. Pau[...]

Whenever you decide to circle back to CI/CD, runners, or your Ubuntu server configuration, just reach out.

To set up email notifications (for password resets, invitations, pipeline alerts, etc.) on a self-hosted GitLab instance, you need to configure an external SMTP server in your GitLab configuratio[...]

Step 1: Edit the GitLab Configuration File

Open your Omnibus GitLab configuration file using a text editor:

sudo nano /etc/gitlab/gitlab.rb

Scroll down or search for the SMTP settings section (or paste them at the bottom of the file). Here is a standard configuration template using a generic SMTP provider (like SendGrid, Amazon SES, [...]

gitlab_rails['smtp_enable'] = true

gitlab_rails['smtp_address'] = "smtp.yourprovider.com"

gitlab_rails['smtp_port'] = 587

gitlab_rails['smtp_user_name'] = "your_smtp_username"

gitlab_rails['smtp_password'] = "your_smtp_password"

gitlab_rails['smtp_domain'] = "yourdomain.com"

gitlab_rails['smtp_authentication'] = "login"

gitlab_rails['smtp_enable_starttls_auto'] = true

gitlab_rails['smtp_tls'] = false

  

# Customize the 'From' address displayed in emails

gitlab_rails['gitlab_email_from'] = 'gitlab@yourdomain.com'

gitlab_rails['gitlab_email_reply_to'] = 'noreply@yourdomain.com'

Step 2: Apply the Changes

Save and close the file, then tell GitLab to reconfigure and apply the new settings:

sudo gitlab-ctl reconfigure

(Note: This step may take a minute or two as GitLab updates its internal services).

Step 3: Test Email Delivery

To verify that your SMTP configuration works correctly, you can trigger a test email directly from the GitLab Ruby console.

1. Open the Rails console:sudo gitlab-rails console
2.   
3. Once inside the console (irb), run the following command (replace with your personal email address):Notify.test_email('your-real-email@example.com', 'Hello from GitLab', 'This is a test messag[...]

