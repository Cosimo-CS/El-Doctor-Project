# El-Doctor-Project

## Linux project 3


- Module: **Scripting** 
- Competence: `is able to write a custom script to monitor machines`
- Type of Challenge: `Consolidation`
- Duration: `3 day`
- Deadline: `12/04/2024`
- Participants: : `solo`

## The mission

There exist a plethora of amazing monitoring tools out there, some of which go as far as offering a full blown graphical dashboard collecting metrics on your entire system in a single unified interface, isn't it great?! Well, this challenge will have you throw all those pre-made solution out the window to **create your own** monitoring script!

You will have multiple days to make it as **useful** (_collect the data you want or need_) and **fancy** (_interactive interface_, _features_, _..._) as possible, the goal is for you to **be creative** and **make it your own**! As such, we won't give you clear instructions to follow nor specific features to implement. Still, we are no monster so here are some idea to inspire you:

- Make an interactive [curses interface](https://en.wikipedia.org/wiki/Curses_(programming_library)) (_or similar_) for your script.
- Deploy your script on a machine you manage and use something like [cron](https://en.wikipedia.org/wiki/Cron) to execute once an hour.
- Collect metrics every hour and store them in an [CSV file](https://en.wikipedia.org/wiki/Comma-separated_values).
- If the state of the machine is critical (_not enough ram_, _..._), notify yourself by mail.
- Send yourself a system report once a week.

In order to validate this challenge, you must have a repository containing your script and its documentation along with your script. 

------------------------------------------------------------------------

Having seen the basics of monitoring during project nr 2, I went straight to work on my script, which I worked on as and when I needed it and the information I needed to provide.

# Description of the full script part by part.


## Function to collect CPU usage

    get_cpu_usage() {

    cpu_percent=$(top -bn1 | awk '/Cpu\(s\)/{print $2}')

    cpu_usage=$(printf "%.0f" "$cpu_percent")

    echo "$cpu_usage%"

    }
------------------------
- get_cpu_usage(): Shell function declaration. Defines the function named "get_cpu_usage";
- { : Opening curly brace indicating the beginning of the function body;
  
### cpu_percent=$(top -bn1 | awk '/Cpu\(s\)/{print $2}'): This command collects CPU usage information using the top command. Here's what it does:

- top -bn1: Runs the top command in batch mode (-b), for one iteration only (-n1).
- |: Pipe operator, redirects the output of the top command to the input of the awk command.
- awk '/Cpu\(s\)/{print $2}': Uses awk to search for lines containing "Cpu(s)" and prints the second field (which contains the CPU usage percentage). The awk pattern /Cpu\(s\)/ looks for lines containing "Cpu(s)" (the backslashes are used to escape parentheses in the regular expression).
- cpu_percent=$(...): Assigns the output of the awk command (CPU usage percentage) to the variable cpu_percent.

------------------------
## Function to collect memory usage

    get_memory_usage() {

    memory_percent=$(free -m | awk 'NR==2{print $3*100/$2}')

    echo "$memory_percent%"

    }



## Function to collect disk usage

    get_disk_usage() {

    disk_usage_percent=$(df -h --output=pcent / | awk 'NR==2{sub("%","",$1); print $1}')

    echo "$disk_usage_percent%"

    }



## Function to get detailed information about users logged in

    get_users_details() {

    users_details=$(who | awk '{print $1 " " $3 " " $4}')

    echo "$users_details"

    }



## Function to get number of users logged in

    get_users_logged_in() {

    users_logged_in=$(who | wc -l)

    echo "$users_logged_in"

    }



## Function to get top 10 CPU-consuming processes filtered by process name

    get_top_cpu_processes() {

    top_processes=$(top -bn1 | awk '/^ *[0-9]+/ {print $9,$12}' | sort -nr | head -n 10)

    echo "$top_processes"

    }







