# GitHub Contribution Guidelines
## Workflow
1. Use **GitHub Issues** as a task tracker. Assign tasks with assigneees. 
2. Before doing any work, **update/sync your local 'main' branch from GitHub.**
3. To work on a task, **create a new branch locally**.
4. **Make sure you are on that branch**. You can then do your work and **do commits there**.
5. After you are done, **publish your local task branch to GitHub**.
6. In order to merge your branch into the 'main' branch, **create a pull request**.
7. **Link the issue** to the pull request by **including "`Closes #<issue number>`" in the PR description**. 
8. You or another reviewer will **review the pull request and merge it into main**. 
9. After the pull request is merged, **mark the issue/task as completed if GitHub hasn't automatically done so.**. 
10. **Delete the task branch after merging**. To start another task, go back to step 1. 

## Naming Guidelines
### Topic
| Category              | Prefix     |
| --------------------- | ---------- |
| Content               | `content`  |
| Slide materials       | `slides`   |
| Handbook materials    | `handbook` |
| Video materials       | `video`    |
| Small fixes           | `fix`      |
| Project documentation | `docs`     |
| Logs                  | `logs`     |

### Format:
#### Branch name
`<topic>/<issue number>-<task id>-<short description>`
All lowercase, with hyphens (-) instead of spaces.
Example: `content/12-s3-01-chapter-1`

#### Commit name
`<topic>: <short description> (#<issue-number>)`
Topic is lowercase. Short description can be any case. Includ the issue number where applicable.
Example: `content: Completed the first part of chapter 1 (#12)`

#### Pull request name
`<topic>: <short description>`
Topic is lowercase. Short description must include the task ID and task name
Example: `content: Completed Task S3-01 Chapter 1`

#### Pull request description
Must include `Closes #<issue-number>`!
Example: 
``` Closes #12
- Added the Chapter 1 description in Markdown.
- Checked grammar, clarity, and formatting.
- Reviewed the content against the task requirements. 
```

#### Issue title
`<topic>: <task id> - <short description or task name>
Example: `content: S3-01 — Write Chapter 1 description`