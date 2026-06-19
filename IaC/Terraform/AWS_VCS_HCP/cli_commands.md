# 1. Create Github repo
- git add
- Git Commands
- git add .
- git commit -m
- git push
# 2. HCP Migrate VCS Workflow
- dev
# 3. In Github Add development branch
- Use VCS to deploy DEVELOPMENT
- git branch -f
- git checkout development
-git branch
-git status
- terraform init -backend-config=dev.hcl -reconfigure
- terraform validate
- terraform plan
# 4. CAN NOT do..
- terraform apply
# 5. Git Commands
- git status
- git add .
- git commit -m “remove extra server & refactor outputs”
- git push
# 6. HCP
- Approve
# 7. Github
- Review development branch to main
# 8. Use VCS to deploy PRODUCTION
- Github
- Merge pull request
- HCP
- Automaticlaly KO pipeline & approve
- Github & HCP
- See the MR merged/approved
# 9. AWS Console
- Review new resources added or destroyed