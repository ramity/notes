# Truss

- Login system
  - userController
    - register
    - login
    - index
    - create
    - edit
    - show
    - delete
  - teamController
    - index
    - create
    - edit
    - show
    - delete
  - projectController
    - index
    - create
    - edit
    - show
    - delete
  - repoController
    - index
    - create
    - edit
    - show
    - delete
  - taskController
    - index
    - create
    - edit
    - show
    - delete
  - dashboardController
    - index
    - create
    - edit
    - show
    - delete
  - messageController
    - index
    - create
    - edit
    - show
    - delete
  - serverController
    - index
    - create
    - edit
    - show
    - delete



Entity stuff:

- user
  - id
  - firstName
  - lastName
  - email
  - displayName
  - password
  - loginSessions(1=>M)
  - sshKeys(1=>M)
  - notifications (1=>M)
- loginSession
  - id
  - user
  - time
  - token
  - userAgent
  - ipAddress

---

- User
  - ID
  - firstName
  - lastName
  - password
  - imageUrl
  - salt
  - dateCreated
  - devices (R)
  - sessions (R)
  - notifications (R)
- Team
  - ID
  - name
  - imageUrl
  - dateCreated
  - repos (R)
  - projects (R)
  - users (R)
- Repo
  - ID
  - name
  - teams (R)
  - projects (R)
  - users (R)
- Project
  - ID
  - Name
  - Description
  - Icon
  - Color
  - Tags
  - viewUsers
  - editUsers
  - joinableUsers
  - Workboards (R)
    - ID
    - Users (R) - who has access to this workboard
    - Tasks (R)
  - Milestones
  - Subprojects
- Policies
  - ID
  - name
  - icon/imageUrl
  - users

EX: All users, admins, no one, etc



Repos can have many projects

Projects can have many repos

Teams can have many projects

Projects can have many teams



- Notifications
  - ID
  - description
  - dateCreated
  - seen = false (default)




---

# Bringing new ideas to the project workflow

- integrated "task poker". On tasks, comments should be labeled to better explain their contents.

  - Specify clarification request

  - Similarly to arcanist, a git wrapper will allow for a better, but more opinionated, git experience.

    - I suppose raft would be a good name. Short for rafter of course.

    - ```
      raft add .
      # calls git add .

      raft review
      # creates review if none found from currently accessed branch against master

      raft review origin develop
      # creates review if none found from currently accessed branch against remote branch 'develop'

      raft review create origin develop
      # creates review from currently accessed branch against remote branch 'develop'

      raft review update origin develop
      # updates review from currently accessed branch against remote branch 'develop'

      # raft diff calls nano/vim (required deps) to create a diff. Very similar to arcanist.
      ```

    - Linters will be something that will need to be researched into significantly as this project develops.

      - As far as I've seen with the little research I've done thus far, it seems I could easily make a .raftlint file similar to .arclint to call an executable any time a user is technically pushing to the configured truss host. That may be the best course of action for the time being.

- Requirements:

  - Code review

  - Good UI

  - Remote team collaboration

  - Features

  - Bulletproof secure extendable API

  - Symfony CMS approach.
    - **Easy to extend**

  - Documentation
    - Integrated markdown editor (w/ live preview)

  - Task interface

  - Task types
    - Bug
    - Feature request

  - Integrated voting system

  - Integrated calendar (plan sprints + see progress overtime)

  - Build in slack clone
    - Default added/created team and project channels.
    - Function similarly to slack in channel management + individual comms.

  - Project management
    - Dashboard management
      - UI management
    - Workboard management
      - Task management

  - Tasks can have weight that can be debated by any members who can view the tasks

  - TrackDuck similar approach for assigned tasks
    - https://goo.gl/CihCmv

  - Integrated time tracker
    - Toggl for inspiration

    - ```bash
      # start integrated timer
      raft timer start

      # stop integrated timer
      raft timer stop

      # open link to webpage timer GUI
      raft timer link

      # show current statistics in shell
      raft timer status

      # set title
      raft timer set title "SAMPLE TEXT"

      # set task
      raft timer set task T526

      # set project
      raft timer set project P253
      ```

  - cpp binary that runs as a process that enables users to see your progress. Shooting for real time application in which git is leveraged to determine how much work. Collab sessions enables a script that watches all files contained in the root directory for changes. When a file's last modified date is more recent than it's last saved date, it automatically pushes the output of `git diff HEAD > file.out` to the server where it is applied to a repo associated with the username of the user that pushed the code. `USERNAME-collab`. The contents in which are viewable via the web based gui. This process can be done by combining the following link (https://stackoverflow.com/a/4610846) and `scp`-ing the file to the backend server. The process of watching the directory for file changes can be done with the following script (https://gist.github.com/senko/1154509) but a dep free script may require more searching. One could dig through the assetic:watch command to determine more information as to how that works (https://github.com/symfony/assetic-bundle/blob/master/Command/WatchCommand.php). If anything a cpp file could be started into a process that watches the directory, but stated under speculation entirely.

    - ```bash
      # start a collab session
      raft collab start

      # stop a collab session
      raft collab stop
      ```

- ```bash
  git diff HEAD
  # shows all local updates since last commit
  ```

- Similarly to arcanist, a token must be set to allow for the script to act on the user's behalf.

  - ```bash
    raft set token "nuGOesD42CKmpZCkBsqxZ2zUkae3gTeN"
    ```

- User registration page
  - require email
  - require configuration setting in environment to toggle open registration
    - open registration = any email can be used
    - open request = must be either invited OR a request must be accepted (via email)
    - invite only = must be invited by a user (via email)
    - closed = no one new can register
  - During registration, the user is asked to download the raft.sh file and add it to their path
    - Upon completion, the user is asked to run the following command: `raft haze [url_to_truss_backend].com/api/haze [token]`
      - Running this command will set the currently selected server to [url_to_truss_backend].com, hit an api enpoint on the truss_backend server via curl by doing something along the lines of: `$confirmToken = curl -H "Content-Type: application/json" -X POST -d '{"token":[token]}' [url_to_truss_backend]/api/haze`. After hitting the endpoint and receiving the confirmToken, the script continues by then (generating a ssh-key)[https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/] named truss. Then the truss.pub is curl-ed to the server (to be added to the authorized_keys file) along side the confirmToken (to validate whom is attempting to add there key). The user is finally prompted with a message to add the generated ssh-key to his keychain via ssh-add.
        - Commands covered:
          - `raft haze [url_to_truss_backend].com/api/haze [token]`
            - Haze in the new guy by taking care of ssh key addition.
          - `raft server select [url_to_truss_backend]`
            - Sets the currently globally selected server in which information is set to exchange.
            - If the current directory has a .raftconfig file, the values listed in the file superscede that set globally.
  - Before moving on, it's important to note that all commands thus far have been technically posting information
  - After registration, the user has the ability to do the following:
    - Create a new repo:
      - During this process, the user has a few options:
        - `raft create [repo/project/review]`

    ​
