# CI/CD with Github actions

Having a CI/CD pipeline setup for any project is one of the best thing you can do for yourself/your team;
- saves you the stress of going back-and-forth, from your local-machine --> server --> run deploy.sh
- and CI runs all your tests automatically, if you have your tests written, you've reduced the chances of your application deploying a bug to production(to users)
- you can do alot of things, like linting tests
  - like when your team member doesn't properly indent codes
  - they prefer 2 / 4 spaces, or even tabs to spaces
- send messages to the necessary channels when things break, you can notify someone

# The HOW?

## Adding A runner

1. First thing, you need to have a github repository.
2. Navigate to repo > settings > actions > runners
3. Add a new runner, depending on your OS
4. Configure the runner to work on your server

## Using the runner

Now that you have the runner;

1. Create a folder in your repository with a `.yml` file
   ```tree
   .github/
    └── workflows
      └── ci-cd.yml
   ```
2. In `ci-cd.yml`, add;
  ```yml
    name: CI

    on:
      push:
        branches: [main]
      pull_request:
        branches: [main]

    jobs:
      build_deploy:
        runs-on: self-hosted

        steps:
          - name: Build files
            run: |
              docker-compose build --force-rm

          - name: Start/Reload Docker
            run: |
              docker-compose up -d
  ```
3. The first line, represents the title of the action
  ```yml
    ...
    name: CI
    ...
  ```
4. The next part is specifies when the action should run, like an event handler, that one says;
    
  ```mermaid
  graph LR
    A[Can i run the action?] --> B{Is this a Pull requests / Push?};
    B -- Yes --> C{Is this the main branch?};
    C -- Yes --> D[Run action];
    B -- No --> E[Do no run action];
    C ----> E[End];
    D ----> E[End];
  ```
  Like this
  ```yml
    ...
    name: CI
    
    on:
      push:
        branches: [main]
      pull_request:
        branches: [main]
    ...
  ```
5. Next you have the jobs section where you define each phase of you action;
  for instance, say you want to make a `build` of your projects and `deploy`/`release` it, you can do that in your steps, in this example,
  i'll just use a single step, `build_deploy`; for each job, you need to specify a runner (use the one you added -> self-hosted), in each job, you'll have steps;
  
    ```yml
    name: CI
    
    on:
      push:
        branches: [main]
      pull_request:
        branches: [main]
      
    jobs:
        build_deploy:
          runs-on: self-hosted

          steps:
            - name: Build files
              run: |
                docker-compose build --force-rm

            - name: Start/Reload Docker
              run: |
                docker-compose up -d
    ```
6. And that's all, you would have your files on your server with all your preconfigured configs, blah 😄

Happy `DevOps`-ing!
