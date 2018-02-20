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

      # (opens or outputs to CLI) link to webpage timer GUI
      raft timer link

      # show current statistics in shell
      raft timer status

      # set title
      raft timer set title "SAMPLE TEXT"

      # set task
      raft timer set task T526

      # set project
      raft timer set project P253

      # set team
      raft timer set team "team-supreme"
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

- Encryption idea - Not exactly polished in current implementation, but an idea non-the-less:

  - Have the user make their password. Once it is passed to the server, the password is encrypted with a salt. The date created is then used as an additional step in such a way that:

    - ```php
      // Given $dateCreated = "01/31/2018"
      $bits = explode("/", $dateCreated);

      $m = settype($bits[0], "integer");
      $d = settype($bits[1], "integer");
      $y = settype($bits[2], "integer");

      // split year to 2 (2 character) variables
      $y = explode("", $y);
      $ya = $y[0] + $y[1];
      $yb = $y[2] + $y[3];

      // parts of hash - hehe
      $kief = explode("", $hash);

      $kief[$m] = (strlen($kief[$m] + 1) > strlen($kief[$m])) ? 'a' : $kief[$m] + 1;
      $kief[$d] = (strlen($kief[$d] + 1) > strlen($kief[$d])) ? 'a' : $kief[$d] + 1;
      $kief[$ya] = (strlen($kief[$ya] + 1) > strlen($kief[$ya])) ? 'a' : $kief[$ya] + 1;
      $kief[$yb] = (strlen($kief[$yb] + 1) > strlen($kief[$yb])) ? 'a' : $kief[$yb] + 1;

      //combine kief back into hash
      $hash = implode("", $kief);
      ```

- Now would be a good time to go ahead and discuss how the backend server will handle the git repo directory structure. This will be a pretty integral part of the version control aspects of the server.

  - ```bash
    # root directory
    /git/repo/

    # repo with ID = 1 ie repo = md5(ID)
    /git/repo/C4CA4238A0B923820DCC509A6F75849B/
    ```

  - This will require that raft will wrap all git functionality in the sense that:

    - ```bash
      # clone a repo 352 into ./[repo_name]
      raft clone R352

      # User will have the option of specifying a wide range of options for cloning repos: repos IDs like the one specified, repo name, hash ID, etc. This is because a curl request like the following gets the specified repo name:

      # in the background the script will preform the following:
      $repoName = curl [selected server] [api endpoint to search whois: R352]?

      # Next the script should check if ./$repoName exists AND if it is empty or not. The logic listed here: https://stackoverflow.com/a/20538655 can help explain the process in either result.

      # in the background the script will also perform the following:
      $repoPath = curl [selected server] [api endpoint to search pathof: R352]

      # After getting the result back from the curl request, the repoName is used to perform the following git clone request:
      git clone git@[backend_ip]:$repoPath $repoName
      ```

- At this point, raft wraps all of git. It's worth mentioning that this approach is highly dependent on git being a specific version in the case that certain functions are different in different versions. This will require that calling functions that function differently in later/newer versions will check if the git version installed on the computer meets a criteria. By doing so, one could specify the functionality of raft. This layer of abstraction will add more work for the developer to support many to all git functions, but it will allow for an easy fix.

- ​