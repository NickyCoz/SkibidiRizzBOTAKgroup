# Simplified GitHub Contribution Guidelines
## Workflow
1. Sync local repository with GitHub.
2. To work on a task, **create a new branch locally**.
3. **Make sure you are on that branch**. You can then do your work and **do commits there**.
4. After you are done, **publish your local task branch to GitHub**.
5. In order to merge your branch into the 'main' branch, **create a pull request**.
6. You or another reviewer will **review the pull request and merge it into main**. 
7.  **Delete the task branch after merging**. To start another task, go back to step 1. 

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
`<topic>/<short description>`  
All lowercase, with hyphens (-) instead of spaces.  
Example: `content/chapter-1`  

#### Commit name
`<topic>: <short description>`  
Topic is lowercase. Short description can be any case. 
Example: `content: Completed the first part of chapter 1`  

#### Pull request name
`<topic>: <short description>`  
Topic is lowercase. 
Example: `content: Completed task 1`  