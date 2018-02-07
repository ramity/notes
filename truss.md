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
  - cpp binary that runs as a process that enables users to see your progress. Shooting for real time application in which git is leveraged to determine how much work

- ```bash
  git add .
  git diff HEAD
  # shows all local updates since last commit
  ```

- ​