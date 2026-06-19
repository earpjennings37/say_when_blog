# 1. Create Falco Rules File to Scan the Container
- SSH
a. Vi nginx-rules.yaml
- rule
- desc
- condition
- output
- priority

# 3. Run Falco to Obtain a Report of ALL the Activity:
- Sudo falco -r nginx-rules.yaml -M -45
- Sudo falco -r nginx-rules.yml -M – 45 > falco-report.log
- 