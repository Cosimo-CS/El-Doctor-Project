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

# Description of the full script part by part.

Having seen the basics of monitoring during project nr 2, I went straight to work on my script, which I worked on as and when I needed it and the information I needed to provide.

## Function to collect CPU usage

    get_cpu_usage() {

    cpu_percent=$(top -bn1 | awk '/Cpu\(s\)/{print $2}')

    cpu_usage=$(printf "%.0f" "$cpu_percent")

    echo "$cpu_usage%"

    }
------------------------
- get_cpu_usage(): Shell function declaration. Defines the function named "get_cpu_usage";
- { : Opening curly brace indicating the beginning of the function body;
  
### cpu_percent=$(top -bn1 | awk '/Cpu\(s\)/{print $2}'): 
- This command collects CPU usage information using the top command. Here's what it does:

- top -bn1: Runs the top command in batch mode (-b), for one iteration only (-n1).
- |: Pipe operator, redirects the output of the top command to the input of the awk command.
- awk '/Cpu\(s\)/{print $2}': Uses awk to search for lines containing "Cpu(s)" and prints the second field (which contains the CPU usage percentage). The awk pattern /Cpu\(s\)/ looks for lines containing "Cpu(s)" (the backslashes are used to escape parentheses in the regular expression).
- cpu_percent=$(...): Assigns the output of the awk command (CPU usage percentage) to the variable cpu_percent.

### cpu_usage=$(printf "%.0f" "$cpu_percent"): 
- This command formats the CPU usage percentage as an integer (rounding to the nearest whole number). Here's what it does:

- printf "%.0f" "$cpu_percent": Uses printf to format the value of cpu_percent as a floating-point number with zero decimal places (%.0f). This effectively rounds the value to the nearest whole number.
- cpu_usage=$(...): Assigns the formatted CPU usage percentage to the variable cpu_usage.
- echo "$cpu_usage%": This command echoes (prints) the formatted CPU usage percentage with a "%" symbol appended to it.
- }: Closing curly brace indicating the end of the function body.

------------------------
## Function to collect memory usage

    get_memory_usage() {

    memory_percent=$(free -m | awk 'NR==2{print $3*100/$2}')

    echo "$memory_percent%"

    }
------------------------
- get_memory_usage(): This is a shell function declaration. It defines a function named get_memory_usage.
- {: Opening curly brace indicating the beginning of the function body.

### memory_percent=$(free -m | awk 'NR==2{print $3*100/$2}'): 
- This command calculates the memory usage percentage. Here's what it does:

- free -m: Runs the free command to display the amount of free and used memory in megabytes (-m).
- |: Pipe operator, redirects the output of the free command to the input of the awk command.

### awk 'NR==2{print $3*100/$2}': 
- Uses awk to process the second line (NR==2) of the free command output. It calculates the memory usage percentage by dividing the used memory ($3) by the total memory ($2), then multiplying by 100 to get a percentage.

- memory_percent=$(...): Assigns the calculated memory usage percentage to the variable memory_percent.
- echo "$memory_percent%": This command echoes (prints) the memory usage percentage with a "%" symbol appended to it.
- }: Closing curly brace indicating the end of the function body.
------------------------
## Function to collect disk usage

    get_disk_usage() {

    disk_usage_percent=$(df -h --output=pcent / | awk 'NR==2{sub("%","",$1); print $1}')

    echo "$disk_usage_percent%"

    }
------------------------
- get_disk_usage(): This is a shell function declaration. It defines a function named get_disk_usage.
- {: Opening curly brace indicating the beginning of the function body.

### disk_usage_percent=$(df -h --output=pcent / | awk 'NR==2{sub("%","",$1); print $1}'): 
- This command retrieves the disk usage percentage for the root filesystem ("/"). Here's what it does:

- df -h --output=pcent /: Runs the df command to display information about the disk space usage for the root filesystem ("/"). The -h option formats sizes in a human-readable format, and the --output=pcent option specifies that only the percentage of disk usage should be output.
- |: Pipe operator, redirects the output of the df command to the input of the awk command.

### awk 'NR==2{sub("%","",$1); print $1}': 
- Uses awk to process the second line (NR==2) of the df command output. It removes the "%" symbol from the first field ($1) using the sub() function, then prints the modified field. This field contains the disk usage percentage.

- disk_usage_percent=$(...): Assigns the disk usage percentage to the variable disk_usage_percent.
- echo "$disk_usage_percent%": This command echoes (prints) the disk usage percentage with a "%" symbol appended to it.
- }: Closing curly brace indicating the end of the function body.
------------------------
## Function to get detailed information about users logged in

    get_users_details() {

    users_details=$(who | awk '{print $1 " " $3 " " $4}')

    echo "$users_details"

    }
------------------------
- get_users_details(): This is a shell function declaration. It defines a function named get_users_details.
- {: Opening curly brace indicating the beginning of the function body.

### users_details=$(who | awk '{print $1 " " $3 " " $4}'): 
- This command collects detailed information about users who are currently logged in. Here's what it does:

- who: Runs the who command to display information about currently logged-in users.
- |: Pipe operator, redirects the output of the who command to the input of the awk command.
- awk '{print $1 " " $3 " " $4}': Uses awk to process each line of the who command output. It prints the first field (username, $1), the third field (terminal or display where the user is logged in, $3), and the fourth field (time of login, $4), separated by spaces.
- users_details=$(...): Assigns the collected user details to the variable users_details.
- echo "$users_details": This command echoes (prints) the collected user details.
- }: Closing curly brace indicating the end of the function body.
------------------------
## Function to get number of users logged in

    get_users_logged_in() {

    users_logged_in=$(who | wc -l)

    echo "$users_logged_in"

    }
------------------------
- get_users_logged_in(): Shell function declaration. It defines a function named get_users_logged_in.
- {: Opening curly brace indicating the beginning of the function body.

### users_logged_in=$(who | wc -l): 
- This command counts the number of currently logged-in users. Here's what it does:

- who: Executes the who command to list the currently logged-in users.
- |: Pipe operator, redirects the output of the who command to the input of the wc command.
- wc -l: Counts the number of lines in the input.
- users_logged_in=$(...): Assigns the count of logged-in users to the variable users_logged_in.
- echo "$users_logged_in": This command echoes (prints) the count of logged-in users.
- }: Closing curly brace indicating the end of the function body.
------------------------
## Function to get top 10 CPU-consuming processes filtered by process name

    get_top_cpu_processes() {

    top_processes=$(top -bn1 | awk '/^ *[0-9]+/ {print $9,$12}' | sort -nr | head -n 10)

    echo "$top_processes"

    }
------------------------
- get_top_cpu_processes(): This is a shell function declaration. It defines a function named get_top_cpu_processes.
- {: Opening curly brace indicating the beginning of the function body.

### top_processes=$(top -bn1 | awk '/^ *[0-9]+/ {print $9,$12}' | sort -nr | head -n 10)
- This command retrieves information about the top 10 CPU-consuming processes. Here's what it does:

- top -bn1: Runs the top command in batch mode (-b) for one iteration only (-n1).
- |: Pipe operator, redirects the output of the top command to the input of the awk command.

### awk '/^ *[0-9]+/ {print $9,$12}':

- Uses awk to process lines starting with one or more spaces followed by digits (/^ *[0-9]+/). It prints the ninth and twelfth fields, which typically contain the CPU usage and process name, respectively.

- |: Pipe operator, redirects the output of the awk command to the input of the sort command.
- sort -nr: Sorts the input numerically (-n) in reverse order (-r). This sorts the CPU usage in descending order, ensuring the most CPU-consuming processes appear first.
- |: Pipe operator, redirects the output of the sort command to the input of the head command.
- head -n 10: Outputs the first 10 lines of the sorted output, which represent the top 10 CPU-consuming processes.
- top_processes=$(...): Assigns the information about the top CPU-consuming processes to the variable top_processes.

- echo "$top_processes": This command echoes (prints) the information about the top CPU-consuming processes.
- }: Closing curly brace indicating the end of the function body.
------------------------




