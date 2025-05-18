# Jenkins Cheat Sheet

## Overview
- **What is Jenkins?**: An open-source automation server for CI/CD pipelines.
- **Pipeline**: A series of automated steps (e.g., build, test, deploy) defined in a `Jenkinsfile`.
- **Two Syntaxes**:
  - **Declarative**: Structured, beginner-friendly (requires Pipeline: Declarative Plugin).
  - **Scripted**: Flexible, Groovy-based.

---

## General Concepts

### Declarative vs. Scripted Pipeline
- **Declarative**:
  - Simplified, opinionated syntax.
  - Wrapped in `pipeline { }`.
  - Example:
    ```groovy
    pipeline {
        agent any
        stages {
            stage('Build') {
                steps {
                    echo 'Building...'
                }
            }
        }
    }
    ```
- **Scripted**:
  - Groovy-based, flexible.
  - Example:
    ```groovy
    node {
        stage('Build') {
            echo 'Building...'
        }
    }
    ```
- **Use Case**: Declarative for standard workflows; Scripted for complex logic.

### Jenkinsfile
- **Purpose**: Defines the pipeline in version control (e.g., Git).
- **Benefits**: Code review, audit trail, single source of truth.
- **Example**:
  ```groovy
  pipeline {
      agent any
      stages {
          stage('Build') {
              steps {
                  sh 'make'
              }
          }
          stage('Test') {
              steps {
                  sh 'make test'
              }
          }
      }
  }
  ```

---

## Pipeline Syntax (Declarative)

### Sections
- **agent**: Where the pipeline/stage runs.
  - **Types**: `any`, `none`, `label`, `node`, `docker`, `dockerfile`, `kubernetes`.
  - **Example**:
    ```groovy
    pipeline {
        agent { docker 'maven:3.9.3-eclipse-temurin-17' }
        stages {
            stage('Build') {
                steps {
                    sh 'mvn clean package'
                }
            }
        }
    }
    ```
  - **Stage-Level**:
    ```groovy
    pipeline {
        agent none
        stages {
            stage('Build') {
                agent { docker 'maven:3.9.3' }
                steps {
                    sh 'mvn --version'
                }
            }
        }
    }
    ```

- **post**: Post-execution steps.
  - **Conditions**: `always`, `failure`, `success`, etc.
  - **Example**:
    ```groovy
    pipeline {
        agent any
        stages {
            stage('Test') {
                steps {
                    sh 'exit 1'
                }
            }
        }
        post {
            always {
                echo 'Done'
            }
            failure {
                mail to: 'team@example.com', subject: 'Failed'
            }
        }
    }
    ```

- **stages**: Contains `stage` directives.
  - **Example**:
    ```groovy
    pipeline {
        agent any
        stages {
            stage('Build') {
                steps {
                    echo 'Building'
                }
            }
            stage('Test') {
                steps {
                    echo 'Testing'
                }
            }
        }
    }
    ```

- **steps**: Actions in a stage.
  - **Example**:
    ```groovy
    pipeline {
        agent any
        stages {
            stage('Example') {
                steps {
                    echo 'Hello World'
                }
            }
        }
    }
    ```

### Directives
- **environment**: Sets variables.
  - **Example**:
    ```groovy
    pipeline {
        agent any
        environment {
            CC = 'clang'
        }
        stages {
            stage('Build') {
                steps {
                    sh 'printenv'
                }
            }
        }
    }
    ```
  - **Credentials**:
    ```groovy
    pipeline {
        agent any
        stages {
            stage('Example') {
                environment {
                    SECRET = credentials('my-secret')
                }
                steps {
                    sh 'echo $SECRET'
                }
            }
        }
    }
    ```

- **options**: Configures pipeline behavior.
  - **Example**:
    ```groovy
    pipeline {
        agent any
        options {
            timeout(time: 1, unit: 'HOURS')
        }
        stages {
            stage('Example') {
                steps {
                    echo 'Hello'
                }
            }
        }
    }
    ```

- **parameters**: User inputs.
  - **Example**:
    ```groovy
    pipeline {
        agent any
        parameters {
            string(name: 'NAME', defaultValue: 'User', description: 'Who to greet?')
        }
        stages {
            stage('Example') {
                steps {
                    echo "Hello ${params.NAME}"
                }
            }
        }
    }
    ```

- **triggers**: Automates retriggering.
  - **Example**:
    ```groovy
    pipeline {
        agent any
        triggers {
            cron('H/15 * * * *') // Every 15 minutes
        }
        stages {
            stage('Example') {
                steps {
                    echo 'Triggered'
                }
            }
        }
    }
    ```

- **stage**: Logical phase.
  - **Example**:
    ```groovy
    pipeline {
        agent any
        stages {
            stage('Build') {
                steps {
                    echo 'Building'
                }
            }
        }
    }
    ```

- **tools**: Auto-installs tools.
  - **Example**:
    ```groovy
    pipeline {
        agent any
        tools {
            maven 'maven-3.9.3'
        }
        stages {
            stage('Build') {
                steps {
                    sh 'mvn --version'
                }
            }
        }
    }
    ```

- **input**: Pauses for user input.
  - **Example**:
    ```groovy
    pipeline {
        agent any
        stages {
            stage('Deploy') {
                input {
                    message "Deploy?"
                    ok "Yes"
                }
                steps {
                    echo 'Deploying'
                }
            }
        }
    }
    ```

- **when**: Conditional execution.
  - **Example**:
    ```groovy
    pipeline {
        agent any
        stages {
            stage('Deploy') {
                when {
                    branch 'production'
                }
                steps {
                    echo 'Deploying'
                }
            }
        }
    }
    ```

### Advanced Constructs
- **Sequential Stages**:
  ```groovy
  pipeline {
      agent none
      stages {
          stage('Sequential') {
              stages {
                  stage('Step 1') {
                      steps {
                          echo 'Step 1'
                      }
                  }
                  stage('Step 2') {
                      steps {
                          echo 'Step 2'
                      }
                  }
              }
          }
      }
  }
  ```

- **Parallel**:
  ```groovy
  pipeline {
      agent any
      stages {
          stage('Parallel') {
              parallel {
                  stage('A') {
                      steps {
                          echo 'A'
                      }
                  }
                  stage('B') {
                      steps {
                          echo 'B'
                      }
                  }
              }
          }
      }
  }
  ```

- **Matrix**:
  ```groovy
  pipeline {
      agent none
      stages {
          stage('Matrix') {
              matrix {
                  axes {
                      axis {
                          name 'OS'
                          values 'linux', 'windows'
                      }
                  }
                  stages {
                      stage('Build') {
                          steps {
                              echo 'Building'
                          }
                      }
                  }
              }
          }
      }
  }
  ```

- **script**: Embeds Scripted code.
  - **Example**:
    ```groovy
    pipeline {
        agent any
        stages {
            stage('Example') {
                steps {
                    script {
                        def x = 1
                        echo "Value: ${x}"
                    }
                }
            }
        }
    }
    ```

---

## Using Docker with Pipeline

### Customizing Execution Environment
- **Purpose**: Use Docker images as stage environments.
- **Example**:
  ```groovy
  pipeline {
      agent {
          docker { image 'node:22.15.1-alpine3.21' }
      }
      stages {
          stage('Test') {
              steps {
                  sh 'node --version'
              }
          }
      }
  }
  ```

### Additional Arguments
- **Purpose**: Pass args to `docker run`.
- **Example**:
  ```groovy
  pipeline {
      agent {
          docker { image 'maven:3.9.3'; args '-v /tmp:/tmp' }
      }
      stages {
          stage('Build') {
              steps {
                  sh 'mvn clean package'
              }
          }
      }
  }
  ```

### Workspace Synchronization
- **Purpose**: Reuse workspace with `reuseNode true`.
- **Example**:
  ```groovy
  pipeline {
      agent any
      stages {
          stage('Build') {
              agent {
                  docker {
                      image 'gradle:8.14.0-jdk21-alpine'
                      reuseNode true
                  }
              }
              steps {
                  sh 'gradle --version'
              }
          }
      }
  }
  ```

### Caching Data
- **Purpose**: Mount volumes for caching.
- **Example**:
  ```groovy
  pipeline {
      agent {
          docker {
              image 'maven:3.9.9-eclipse-temurin-21'
              args '-v $HOME/.m2:/root/.m2'
          }
      }
      stages {
          stage('Build') {
              steps {
                  sh 'mvn -B'
              }
          }
      }
  }
  ```

### Multiple Containers
- **Purpose**: Different images per stage.
- **Example**:
  ```groovy
  pipeline {
      agent none
      stages {
          stage('Back-end') {
              agent { docker 'maven:3.9.9-eclipse-temurin-21-alpine' }
              steps {
                  sh 'mvn --version'
              }
          }
          stage('Front-end') {
              agent { docker 'node:22.15.1-alpine3.21' }
              steps {
                  sh 'node --version'
              }
          }
      }
  }
  ```

### Using a Dockerfile
- **Purpose**: Build custom image from `Dockerfile`.
- **Dockerfile**:
  ```dockerfile
  FROM node:22.15.1-alpine3.21
  RUN apk add subversion
  ```
- **Example**:
  ```groovy
  pipeline {
      agent { dockerfile true }
      stages {
          stage('Test') {
              steps {
                  sh 'node --version'
                  sh 'svn --version'
              }
          }
      }
  }
  ```

### Advanced Usage (Scripted)
- **Sidecar Containers**:
  ```groovy
  node {
      docker.image('mysql:8-oracle').withRun('-e "MYSQL_ROOT_PASSWORD=my-secret-pw"') {
          sh 'make check'
      }
  }
  ```
- **Building Containers**:
  ```groovy
  node {
      def image = docker.build("my-image:${env.BUILD_ID}")
      image.inside {
          sh 'make test'
      }
      image.push()
  }
  ```
- **Remote Docker Server**:
  ```groovy
  node {
      docker.withServer('tcp://swarm.example.com:2376', 'swarm-certs') {
          docker.image('mysql:8-oracle').withRun('-p 3306:3306') {
              /* do things */
          }
      }
  }
  ```
- **Custom Registry**:
  ```groovy
  node {
      docker.withRegistry('https://registry.example.com', 'credentials-id') {
          def image = docker.build("my-image:${env.BUILD_ID}")
          image.push()
      }
  }
  ```

---

## Additional Topics

### Environment Variables
- **Access**: Use `env.VAR`.
- **Set**: Use `environment` block.
- **Example**:
  ```groovy
  pipeline {
      agent any
      environment {
          BUILD_ENV = 'dev'
      }
      stages {
          stage('Example') {
              steps {
                  echo "Env: ${env.BUILD_ENV}"
              }
          }
      }
  }
  ```

### Credentials
- **Types**: Secret Text, Username/Password, Secret File.
- **Example**:
  ```groovy
  pipeline {
      agent any
      stages {
          stage('Example') {
              environment {
                  CRED = credentials('my-cred')
              }
              steps {
                  sh 'echo $CRED'
              }
          }
        }
    }
    ```

### Parameters
- **Define**: Use `parameters` block.
- **Example**:
  ```groovy
  pipeline {
      agent any
      parameters {
          string(name: 'NAME', defaultValue: 'User')
      }
      stages {
          stage('Example') {
              steps {
                  echo "Hello ${params.NAME}"
              }
          }
      }
  }
  ```

### Handling Failure
- **Declarative**: Use `post`.
  - **Example**:
    ```groovy
    pipeline {
        agent any
        stages {
            stage('Test') {
                steps {
                    sh 'exit 1'
                }
            }
        }
        post {
            failure {
                echo 'Failed'
            }
        }
    }
    ```
- **Scripted**: Use `try/catch`.
  - **Example**:
    ```groovy
    node {
        stage('Test') {
            try {
                sh 'exit 1'
            } catch (e) {
                echo 'Failed'
            }
        }
    }
    ```

### Multiple Agents
- **Example**:
  ```groovy
  pipeline {
      agent none
      stages {
          stage('Build') {
              agent any
              steps {
                  sh 'make'
                  stash includes: '**.jar', name: 'app'
              }
          }
          stage('Test') {
              agent { label 'linux' }
              steps {
                  unstash 'app'
                  sh 'make check'
              }
          }
      }
  }
  ```

---

## Quick Tips
- **Debugging**: Check Console Output in Jenkins UI.
- **Replay**: Test changes directly in the UI.
- **Docker Labels**: Configure in **Manage Jenkins > System** for non-Linux agents.
- **macOS PATH**: Update `/usr/local/Cellar/jenkins-lts/XXX/homebrew.mxcl.jenkins-lts.plist` with `/usr/local/bin`.