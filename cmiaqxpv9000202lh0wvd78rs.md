---
title: "How I built my DaC Pipeline"
seoTitle: "Building a DaC Pipeline: My Experience"
seoDescription: "Learn how to build a Detection as Code (DaC) pipeline using Sigma, Terraform, Github Actions, and Splunk. Step-by-step guide included"
datePublished: Sat Nov 22 2025 20:33:17 GMT+0000 (Coordinated Universal Time)
cuid: cmiaqxpv9000202lh0wvd78rs
slug: how-i-built-my-dac-pipeline
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1763737215342/48a958a2-3fe6-43e3-aa28-7f0b7690f393.jpeg
tags: terraform, cybersecurity, homelab, blueteam, dac, sigma, detection-engineering, detection-as-code, detection-rules

---

The moment I learned about Detection as Code(DaC) I knew it was something I wanted to learn so I wasted no time diving in. I spent the next weekend setting up a DaC pipeline foundation that will allow me to expand upon as I get more proficient with it. Coming from a software engineering background DaC incorporates several paradigms Im already deeply familiar with, so I didnt think it would take to long to get up to speed. This article will cover my approach and technologies used, lets get right to it!

# Technologies Used

* Sigma:
    
* Terraform
    
* Github Actions
    
* Self-Hosted Github Runner
    
* Splunk
    

### Sigma

Sigma is a YAML based rule language, used for writing detections once then compiling them for multiple platforms (Splunk, Sentinel, Elastic, etc). Without Sigma detections would have to be sepeatly written for each platform. Sigma will be my single source of truth for detection rules.

**Benefits**:

* Standardized rule format between platforms.
    
* Prevents platform lock in.
    
* Automated rule compilation.
    

### Terraform

Terraform manages infrastructure using code(IaC). Splunk objects can be programmatically created instead of point and click. This leads to reproducible and rebuildable objects that get documented in Github. Terraform automates the deployment of SOC infrastructure.

**Benefits**:

* Automates SOC infrastructure deployment.
    
* Documentation in Github.
    
* Prevents configuration drift.
    

### Github Actions

Github Actions is the automation pipeline providing Continuous Integration/Continuous Development(CI/CD). It runs workflows on triggers, git push, pull requests, schedules, manual. Github Actions will validate Sigma rules, compile them to SPL, run Terraform, and deploy to Splunk.

**Benefits**:

* CI/CD for detections.
    
* Version controlled rules.
    
* Pull request inspection in a team environment/collaboration.
    
* Deployment automation.
    

### Self hosted Github Runner

This is the executer that enables all of the automation to take place. Typically the Runner would be used on Github’s cloud, but to prevent opening ports and exposing my lab to the world I decided to self host locally to keep my environment secure. So there will be no outside access to my lab by running the standard Runner.

**Benefits**:

* Secure connection to on prem Splunk instance.
    
* No need to expose Splunk to the internet.
    
* Private builds.
    

### Splunk

Splunk Enterprise is the Security Information & Event Manager(SIEM). Splunk receives complied SPL(Search Processing Language) to consume. The Splunk API will be listiening on port 8089 in order to receive Knowledge Objects produced by the automation pipeline.

**Benefits**:

* Detection execution.
    
* Alerts and dashboards
    
* Rule debugging and tuning.
    

### Summary

| **Technology** | **Purpose** | **Reason for use** |
| --- | --- | --- |
| Sigma | Write rules once and be compatible with multiple platforms. | Standardized detections that prevent vendor lock in. |
| Terraform | Provision Splunk infrastructure as code. | Reproduce or rebuild infrastructure automatically. |
| Github Actions | CI/CD automation. | Validates, compiles, and deploys rules. |
| Self hosted Runner | Local execution. | Securely reaches on prem Splunk without exposure. |
| Splunk | Runs detections. | Alert generation. |

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763743266614/5cffc6cb-259d-4680-ac43-935c99febff1.png align="center")

# Workflow

1. Write a Sigma rule.
    
2. Create pull request.
    
3. Github Actions executes jobs:
    
    Validates Sigma rule.
    
    Coverts rule to SPL.
    
    Run Terraform.
    
    Populate detection in Splunk.
    

# The Repository

```bash
detections-as-code/
├── rules/
│   ├── windows/
│   ├── network/
│   ├── linux/
│   └── cloud/
├── tests/
│   ├── positive/
│   └── negative/
├── generated/
│   └── splunk/
├── .github/workflows/
│   └── build.yml
└── README.md
```

Above is a representation of the repository structure that will exist as I continue to build this out. Some of the directories are not in Github because they have no files in them yet. Here is the directory breakdown:

* **detections\_as\_code**: this is the root direcotry that contains all of the project files discussed.
    
* **rules**: this directory contains sub directories intended to organize Sigma rules by usecase. Sigma rules written for Windows and Linux will go in their respective directories. Rules for the firewall will go into the network directory and rules for anything cloud related will go into cloud. The network and cloud directories could get renamed in the future and will be on the backburner, they exist here as a type of road map to gueid me as I continue building but the immediate focus will be Windows, Linux and pfSense firewall rules.
    
* **tests**: this directory also wont show up in Github because its temporarily empty. This is intended to house tests to ensure rules work as expected.
    
* **generated**: this is where platform specific compiled resources will be populated by the automation process. For now my focus is on Splunk, with intentions to integrate Elastic in the future.
    
* **.github/workflows**: this is where build.yml lives. This file dictates the automation steps of the CI/CD pipeline and we will delve deeper into the build steps below.
    

# The Automation Pipeline

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763758264494/f389d7c9-a398-44da-8029-31e785143e2f.jpeg align="center")

At the time of this writing, this is what the build.yml file looks like(I fully expect changes in the future as development continues):

```yaml
name: Build Detections
on:
  push:
    branches: ["main"]
  pull_request: {}

jobs:
  build:
    runs-on: [self-hosted, linux, x64]

    env:
      SPLUNK_USERNAME: ${{ secrets.SPLUNK_USERNAME }}
      SPLUNK_PASSWORD: ${{ secrets.SPLUNK_PASSWORD }}
      SPLUNK_URL: ${{ secrets.SPLUNK_URL }}

    # Timeout to prevent hanging jobs
    timeout-minutes: 15
    
    steps:
    # Project Checkout
    - name: Checkout
      uses: actions/checkout@v4

    # Clean workspace before starting
    - name: Clean Workspace
      run: |
        rm -rf generated/

    # Install and configure Sigma with Splunk plugin
    - name: Install Sigma
      run: |
        python3 -m pip install --upgrade pip
        python3 -m pip install sigma-cli
        sigma plugin install splunk

    # Convert Sigma rules into SPL
    - name: Convert Sigma to SPL
      run: | 
        set -e
        mkdir -p generated/splunk
        
        echo "Converting Sigma rules to SPL..."
        rule_count=0
        
        while IFS= read -r rule_file; do
          file_name=$(basename "$rule_file" .yml)
          
          if sigma convert --target splunk --pipeline splunk_windows "$rule_file" > "generated/splunk/${file_name}.spl" 2>&1; then
            if [ -s "generated/splunk/${file_name}.spl" ]; then
              echo "${file_name}.spl"
              rule_count=$((rule_count + 1))
            else
              echo "${file_name}.spl is empty"
              exit 1
            fi
          else
            echo "Failed to convert $rule_file"
            exit 1
          fi
        done < <(find rules -type f -name "*.yml")
        
        if [ "$rule_count" -eq 0 ]; then
          echo "ERROR: No rules converted"
          exit 1
        fi
        
        echo "Converted $rule_count rule(s)"
        ls -lh generated/splunk/

    # Save SPL artifact with the name spl-detections to the generated/splunk directory
    - name: Upload SPL as an artifact
      uses: actions/upload-artifact@v4
      with:
        name: spl-detections
        path: generated/splunk

    # Install Terraform
    - name: Install Terraform
      uses: hashicorp/setup-terraform@v3

    # Terraform formatting check
    - name: Terraform Format
      working-directory: terraform
      run: terraform fmt -check

    # Initialize Terraform
    - name: Terraform Init
      working-directory: terraform
      env:
        TF_VAR_splunk_url: ${{ secrets.SPLUNK_URL }}
        TF_VAR_splunk_username: ${{ secrets.SPLUNK_USERNAME }}
        TF_VAR_splunk_password: ${{ secrets.SPLUNK_PASSWORD }}
        TF_VAR_alert_email: ${{ secrets.ALERT_EMAIL }}
        TF_VAR_insecure_skip_verify: "true"
      run: terraform init

    # Test Terraform before deployment
    - name: Terraform Validate
      working-directory: terraform
      env:
        TF_VAR_splunk_url: ${{ secrets.SPLUNK_URL }}
        TF_VAR_splunk_username: ${{ secrets.SPLUNK_USERNAME }}
        TF_VAR_splunk_password: ${{ secrets.SPLUNK_PASSWORD }}
        TF_VAR_alert_email: ${{ secrets.ALERT_EMAIL }}
        TF_VAR_insecure_skip_verify: "true"
      run: terraform validate

    # Terraform plan
    - name: Terraform Plan
      id: plan
      working-directory: terraform
      env:
        TF_VAR_splunk_url: ${{ secrets.SPLUNK_URL }}
        TF_VAR_splunk_username: ${{ secrets.SPLUNK_USERNAME }}
        TF_VAR_splunk_password: ${{ secrets.SPLUNK_PASSWORD }}
        TF_VAR_alert_email: ${{ secrets.ALERT_EMAIL }}
        TF_VAR_insecure_skip_verify: "true"
      run: |
        terraform plan -out=tfplan
        terraform show tfplan

    # Terraform apply
    - name: Terraform Apply
      working-directory: terraform
      env:
        TF_VAR_splunk_url: ${{ secrets.SPLUNK_URL }}
        TF_VAR_splunk_username: ${{ secrets.SPLUNK_USERNAME }}
        TF_VAR_splunk_password: ${{ secrets.SPLUNK_PASSWORD }}
        TF_VAR_alert_email: ${{ secrets.ALERT_EMAIL }}
        TF_VAR_insecure_skip_verify: "true"
      run: |
        terraform apply tfplan
        
        echo "### Deployment Summary" >> $GITHUB_STEP_SUMMARY
        echo "" >> $GITHUB_STEP_SUMMARY
        echo "**Deployed Detection Rules:**" >> $GITHUB_STEP_SUMMARY
        terraform output -json deployed_detections | jq -r 'to_entries[] | "- **\(.value.name)** (Severity: \(.value.severity))"' >> $GITHUB_STEP_SUMMARY
        echo "" >> $GITHUB_STEP_SUMMARY
        echo "**Total Rules Deployed:** $(terraform output -raw detection_count)" >> $GITHUB_STEP_SUMMARY

# Clean up
    - name: Cleanup
      if: always()
      run: |
        echo "Cleaning up temporary files..."
        rm -rf terraform/.terraform/
        rm -f terraform/tfplan
        rm -f terraform/terraform.tfstate.backup
```

## Build File Breakdown

Lets dissect the build file and cover what each step does.

### Name & Triggers

```yaml
name: Build Detections
on:
  push:
    branches: ["main"]
  pull_request: {}
```

This section defines the name and the triggers that will activate the automation process. The automation will activate `on` a git `push` to the `main` branch in the repository.

### Jobs

```yaml
jobs:
  build:
    runs-on: [self-hosted, linux, x64]

    env:
      SPLUNK_USERNAME: ${{ secrets.SPLUNK_USERNAME }}
      SPLUNK_PASSWORD: ${{ secrets.SPLUNK_PASSWORD }}
      SPLUNK_URL: ${{ secrets.SPLUNK_URL }}

    # Timeout to prevent hanging jobs
    timeout-minutes: 15
```

The `jobs` section is where different automation jobs can be defined, for now there is only one job defined as `build`. In this section of the file we define the Runner, environment variables and the length of our timeout.

As mentioned I opted to use a Self Hosted Runner, if I were using Github cloud `runs-on` would take `ubuntu` or something similar but ive given the tags of my Runner instance: `[self-hosted, linux, x64]`.

I pass in 3 environment variables securely stored in Github Secrets so the Runner can access my Splunk instance: `SPLUNK_USERNAME` , `SPLUNK_PASSWORD` , `SPLUNK_URL` .

The `timeout-minutes` set to 15 means that if the the `build` job ever hangs/stalls for any reason, it will be allowed 15 minutes to try to come to a resolution before canceling and failing the job.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763824103850/94f15265-48ed-4936-940b-5e20ec1a49fa.gif align="center")

### Steps

The `steps` section defines all the automation steps of the job. Each step defines its name, what it does, and whatever might be needed for it to complete. Ill go through each one.

The first step uses Github Actions the do a git checkout and grab and store the project to perform the automatons on.

```yaml
steps:
    # Project Checkout
    - name: Checkout
      uses: actions/checkout@v4
```

The next step cleans up any files from previous job completions by running the command: `rm -rf generated/` . This removes the directory that contains automatically generated artifacts from completion of the job.

```yaml
# Clean workspace before starting
    - name: Clean Workspace
      run: |
        rm -rf generated/
```

Next, Sigma is installed and configured so that the Runner can automate Sigma commands. The Sigma plugin for Splunk is installed to enable Sigma to generate SPL.

```yaml
    # Install and configure Sigma with Splunk plugin
    - name: Install Sigma
      run: |
        python3 -m pip install --upgrade pip
        python3 -m pip install sigma-cli
        sigma plugin install splunk
```

After installing Sigma, the next step converts Sigma rules to SPL. In this step, the previously mentioned `generated` directory gets created with a `splunk` sub directory to store the SPL output files. Other platform specific artifacts will get populated here as well in the future.

As seen in the code snippet below, this step is running a lot of commands. Essentially whats happening here is that the job will create a location to store output files, and communicate through the terminal whats going on and keep track of how many Sigma rules get converted. It creates SPL files with the same name as the Sigma rule as it performs the conversion. It also performs some checks so that if this step goes wrong it can fail gracefully and not hang for 15 minutes.

I will probably incorporate a bash script to encapsulate this functionality so the step isnt as visibly long, but for now this avoids adding complexity.

```yaml
    # Convert Sigma rules into SPL
    - name: Convert Sigma to SPL
      run: | 
        set -e
        mkdir -p generated/splunk
        
        echo "Converting Sigma rules to SPL..."
        rule_count=0
        
        while IFS= read -r rule_file; do
          file_name=$(basename "$rule_file" .yml)
          
          if sigma convert --target splunk --pipeline splunk_windows "$rule_file" > "generated/splunk/${file_name}.spl" 2>&1; then
            if [ -s "generated/splunk/${file_name}.spl" ]; then
              echo "${file_name}.spl"
              rule_count=$((rule_count + 1))
            else
              echo "${file_name}.spl is empty"
              exit 1
            fi
          else
            echo "Failed to convert $rule_file"
            exit 1
          fi
        done < <(find rules -type f -name "*.yml")
        
        if [ "$rule_count" -eq 0 ]; then
          echo "ERROR: No rules converted"
          exit 1
        fi
        
        echo "Converted $rule_count rule(s)"
        ls -lh generated/splunk/
```

When the conversion is done, this next step will compress the SPL files into a .zip file and upload it to Github to make available for download.

```yaml
    # Upload SPL artifact
    - name: Upload SPL as an artifact
      uses: actions/upload-artifact@v4
      with:
        name: spl-detections
        path: generated/splunk
```

The next step is to install Terraform.

```yaml
    # Install Terraform
    - name: Install Terraform
      uses: hashicorp/setup-terraform@v3
```

Once Terraform is installed, it will format the .tf files. What is seen here bellow is checking the files for expected formatting but I have since changed this step to perform the formatting to prevent the build from failing at this step.

```yaml
    # Terraform formatting check
    - name: Terraform Format
      working-directory: terraform
      run: terraform fmt -check
```

The next step initializes Terraform with the variables from Github secrets.

```yaml
    # Initialize Terraform
    - name: Terraform Init
      working-directory: terraform
      env:
        TF_VAR_splunk_url: ${{ secrets.SPLUNK_URL }}
        TF_VAR_splunk_username: ${{ secrets.SPLUNK_USERNAME }}
        TF_VAR_splunk_password: ${{ secrets.SPLUNK_PASSWORD }}
        TF_VAR_alert_email: ${{ secrets.ALERT_EMAIL }}
        TF_VAR_insecure_skip_verify: "true"
      run: terraform init
```

After that Terraform performs validation.

```yaml
    # Test Terraform before deployment
    - name: Terraform Validate
      working-directory: terraform
      env:
        TF_VAR_splunk_url: ${{ secrets.SPLUNK_URL }}
        TF_VAR_splunk_username: ${{ secrets.SPLUNK_USERNAME }}
        TF_VAR_splunk_password: ${{ secrets.SPLUNK_PASSWORD }}
        TF_VAR_alert_email: ${{ secrets.ALERT_EMAIL }}
        TF_VAR_insecure_skip_verify: "true"
      run: terraform validate
```

Next Terraform will plan its rollout and save the plan locally as well as print the plan to the terminal.

```yaml
    # Terraform plan
    - name: Terraform Plan
      id: plan
      working-directory: terraform
      env:
        TF_VAR_splunk_url: ${{ secrets.SPLUNK_URL }}
        TF_VAR_splunk_username: ${{ secrets.SPLUNK_USERNAME }}
        TF_VAR_splunk_password: ${{ secrets.SPLUNK_PASSWORD }}
        TF_VAR_alert_email: ${{ secrets.ALERT_EMAIL }}
        TF_VAR_insecure_skip_verify: "true"
      run: |
        terraform plan -out=tfplan
        terraform show tfplan
```

Once thats done, Terraform puts its plan in action and populates Splunk with Saved Searches derived from previous automation steps, and display a summary on the terminal.

```yaml
    # Terraform apply
    - name: Terraform Apply
      working-directory: terraform
      env:
        TF_VAR_splunk_url: ${{ secrets.SPLUNK_URL }}
        TF_VAR_splunk_username: ${{ secrets.SPLUNK_USERNAME }}
        TF_VAR_splunk_password: ${{ secrets.SPLUNK_PASSWORD }}
        TF_VAR_alert_email: ${{ secrets.ALERT_EMAIL }}
        TF_VAR_insecure_skip_verify: "true"
      run: |
        terraform apply tfplan
        
        echo "### Deployment Summary" >> $GITHUB_STEP_SUMMARY
        echo "" >> $GITHUB_STEP_SUMMARY
        echo "**Deployed Detection Rules:**" >> $GITHUB_STEP_SUMMARY
        terraform output -json deployed_detections | jq -r 'to_entries[] | "- **\(.value.name)** (Severity: \(.value.severity))"' >> $GITHUB_STEP_SUMMARY
        echo "" >> $GITHUB_STEP_SUMMARY
        echo "**Total Rules Deployed:** $(terraform output -raw detection_count)" >> $GITHUB_STEP_SUMMARY
```

The last step of this job performs clean up duties.

```yaml
# Clean up
    - name: Cleanup
      if: always()
      run: |
        echo "Cleaning up temporary files..."
        rm -rf terraform/.terraform/
        rm -f terraform/tfplan
        rm -f terraform/terraform.tfstate.backup
```

I still have things I want to do to this build file, but this is how it looks at the time of writing this post. One of the things I learned while running this build is that I need to preserve state for Terraform to prevent build failures for files that exist prior to running the job. This will make Terraform aware of conversions from previous builds, that id definitly one of the things that held me up while I figured out what was going on that made this post being written up take so long.

I also want to reduce the environment variables used by Terraform from being in every step because coming from a software engineering background the lack of not repeating myself fills me with great shame…

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763840713715/a73a90eb-c2eb-4fac-9dc2-bc48b199a62b.gif align="center")

There are also changes to support the back end storage of Terraform state I had to pause to prevent further delays to publishing this post.

# Conclusion

There are 2 files that get edited that starts of this entire process prior to the pipeline automation:

* The new Sigma rule file that defines the detection that is being added.
    
* locals.tf because I haven’t automated this part of the things yet. So the idea is to have a python script rewrite locals.tf when a new Sigma rule is added to the repository. But I want to make sure I have a solid understanding before I automate this final step to complete full automation.
    
    There is still development ongoing, this is a fun project for me. Learning Sigma and Terraform are the areas I need the most work in, the rest of the technologies used I have plenty of experience in due to their uses in the software development life cycle. I probably will update this post as things progress, like I did with the [Hash Catnip](https://coded-alchemy.github.io/#/hashcatnip) post.
    
    So for now Ill be signing off, I hope you enjoyed this post! Feel free to leave any comments about anything I could do better, Id love to read the feedback.