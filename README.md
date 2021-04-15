# Number of commits per day with Jq

> This repo contains a command requesting GitLab REST API and delivering a number of commits per day.   

## GitLab API
Commits can be obtained via:
```bash
curl --header "PRIVATE-TOKEN: $(your-private-token)" "https://gitlab.rebrainme.com/api/v4/projects/27865/repository/commits?since=2021-03-01T00:00:00Z&until=2021-03-31T23:59:00Z"
```
Private token can be generated via Gitlab > Your profile > Preferences > Access Tokens.

`Since` and `until` attributes can be added to show the number of commits within a month, i.e. March:
```bash
?since=2021-03-01T00:00:00Z&until=2021-03-31T23:59:00Z
```

## Jq
Jq extracts commits dates and creates a dict, where repeating date occurrences are counted.

```shell
jq '[.[].committed_date ] | map 
    ({  
        date: (. | strptime("%Y-%m-%dT%H:%M:%S.000%Z") | todate[0:10]), 
        time: (. | strptime("%Y-%m-%dT%H:%M:%S.000%Z") | todate[11:19]) }) 
        | reduce .[] as $item ( {}; .[$item.date] += 1 )'
```
Use [Jq playground](https://jqplay.org/) to play with this pipeline.  
Try to remove commands following pipes starting at the end of the pipeline to see intermediate results.

## Usage 
### Run the command as one long string:
```shell
curl --header "PRIVATE-TOKEN: $(your-private-token)" "https://gitlab.rebrainme.com/api/v4/projects/27865/repository/commits?since=2021-03-01T00:00:00Z&until=2021-03-31T23:59:00Z" | jq '[.[].committed_date ] | map ({  date: (. | strptime("%Y-%m-%dT%H:%M:%S.000%Z") | todate[0:10]), time: (. | strptime("%Y-%m-%dT%H:%M:%S.000%Z") | todate[11:19]) }) | reduce .[] as $item ( {};   .[$item.date] += 1 )'
```

## Installation  
### Clone this repo

```shell
git clone https://...
```

## Acknowledgments

This repo was inspired by [rebrainme.com](https://rebrainme.com) team

## See Also
- [Jq recipes](https://remysharp.com/drafts/jq-recipes)
- [Jq playground](https://jqplay.org/)
- [Jq manual](https://stedolan.github.io/jq/manual/)
- [GitLab Commits API](https://docs.gitlab.com/ee/api/commits.html)
- [Shell Rounding floating point numbers](https://unix.stackexchange.com/questions/167058/how-to-round-floating-point-numbers-in-shell)
- [Date & time formats](https://devhints.io/datetime)

## License
Follow Ansible, Go, and Terraform licenses terms and conditions.