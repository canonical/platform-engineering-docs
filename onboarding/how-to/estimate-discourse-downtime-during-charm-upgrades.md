# Estimate discourse downtime during charm upgrades
**Source: [Discourse](https://discourse.canonical.com/t/estimate-discourse-downtime-during-charm-upgrades/3267)**

Run this script in parallel with the charm upgrade.
This doesn’t give the exact downtime but just a close estimation most of the time!

```
#!/bin/bash
down=false
down_at=()
down_during=()
index=0
checks=0
url="https://discourse.staging.ubuntu.com"
trap break INT
while :
do
	response=$(curl --write-out '%{http_code}' --silent --output /dev/null ${url})
	if [[ $response != "200" ]]; then
		if [ "$down" = false ]; then
			# Was up but now down
			down_at["$index"]=$(date +%FT%T%Z)
			down_during["$index"]=$(date +%s)
		fi
		down=true
	else
		if [ "$down" = true ]; then
			# Was down but now up
			down_st_time=${down_at["$index"]}
			down_st_time_seconds=$(date --date "$down_st_time" +%s)
			current_time=$(date +%s)
			down_during["$index"]=$(("$current_time" - "$down_st_time_seconds"))
			((index++))
		fi
		down=false
	fi
	((checks++))
	sleep 1
done
trap - INT
if (( ${#down_at[@]} != 0 )); then
    for i in "${!down_at[@]}"; do
		printf "${url} went down at: %s for: %s second(s)\n" "${down_at[$i]}" "${down_during[$i]}"
	done
else
	echo "${url} did not go down during the execution of the script"
fi
```

hitting Ctrl-C will output something like this:

```
tphan025 at ubuntu1 in ~/personal/discouse-dev 
$ ./liveness-check-during-charm-upgrade.sh
^C
https://discourse.staging.ubuntu.com went down at: 2024-02-27T23:11:03CET for: 5 second(s)
https://discourse.staging.ubuntu.com went down at: 2024-02-27T23:11:13CET for: 3 second(s)
```
or
```
tphan025 at ubuntu1 in ~/personal/discouse-dev 
$ ./liveness-check-during-charm-upgrade.sh
^C
https://discourse.staging.ubuntu.com did not go down during the execution of the script
```

