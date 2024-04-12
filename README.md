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

![alt text](/img/script-first-try.png)

## Function to collect CPU usage

    get_cpu_usage() {

    cpu_percent=$(top -bn1 | awk '/Cpu\(s\)/{print $2}')

    cpu_usage=$(printf "%.0f" "$cpu_percent")

    echo "$cpu_usage%"

    }
------------------------
- **_get_cpu_usage()_**: Shell function declaration. Defines the function named "get_cpu_usage";
- **{** : Opening curly brace indicating the beginning of the function body;
  
### cpu_percent=$(top -bn1 | awk '/Cpu\(s\)/{print $2}'): 
- This command collects CPU usage information using the top command. Here's what it does:

- **_top -bn1_**: Runs the top command in batch mode (-b), for one iteration only (-n1).
- **|**: Pipe operator, redirects the output of the top command to the input of the awk command.
- **_awk '/Cpu\(s\)/{print $2}'_**: Uses awk to search for lines containing "Cpu(s)" and prints the second field (which contains the CPU usage percentage). The awk pattern /Cpu\(s\)/ looks for lines containing "Cpu(s)" (the backslashes are used to escape parentheses in the regular expression).
- **_cpu_percent=$(...)_**: Assigns the output of the awk command (CPU usage percentage) to the variable cpu_percent.

### cpu_usage=$(printf "%.0f" "$cpu_percent"): 
- This command formats the CPU usage percentage as an integer (rounding to the nearest whole number). Here's what it does:

- **_printf "%.0f" "$cpu_percent"_**: Uses printf to format the value of cpu_percent as a floating-point number with zero decimal places (%.0f). This effectively rounds the value to the nearest whole number.
- **_cpu_usage=$(...)_**: Assigns the formatted CPU usage percentage to the variable cpu_usage.
- **_echo "$cpu_usage%"_**: This command echoes (prints) the formatted CPU usage percentage with a "%" symbol appended to it.
- **}**: Closing curly brace indicating the end of the function body.

------------------------
## Function to collect memory usage

    get_memory_usage() {

    memory_percent=$(free -m | awk 'NR==2{print $3*100/$2}')

    echo "$memory_percent%"

    }
------------------------
- **_get_memory_usage()_**: This is a shell function declaration. It defines a function named get_memory_usage.
- **{**: Opening curly brace indicating the beginning of the function body.

### memory_percent=$(free -m | awk 'NR==2{print $3*100/$2}'): 
- This command calculates the memory usage percentage. Here's what it does:

- **_free -m_**: Runs the free command to display the amount of free and used memory in megabytes (-m).
- **|**: Pipe operator, redirects the output of the free command to the input of the awk command.

### awk 'NR==2{print $3*100/$2}': 
- Uses awk to process the second line (NR==2) of the free command output. It calculates the memory usage percentage by dividing the used memory ($3) by the total memory ($2), then multiplying by 100 to get a percentage.

- **_memory_percent=$(...)_**: Assigns the calculated memory usage percentage to the variable memory_percent.
- **_echo "$memory_percent%"_**: This command echoes (prints) the memory usage percentage with a "%" symbol appended to it.
- **}**: Closing curly brace indicating the end of the function body.
------------------------
## Function to collect disk usage

    get_disk_usage() {

    disk_usage_percent=$(df -h --output=pcent / | awk 'NR==2{sub("%","",$1); print $1}')

    echo "$disk_usage_percent%"

    }
------------------------
- **_get_disk_usage()_**: This is a shell function declaration. It defines a function named get_disk_usage.
- **{**: Opening curly brace indicating the beginning of the function body.

### disk_usage_percent=$(df -h --output=pcent / | awk 'NR==2{sub("%","",$1); print $1}'): 
- This command retrieves the disk usage percentage for the root filesystem ("/"). Here's what it does:

- **_df -h --output=pcent /_**: Runs the df command to display information about the disk space usage for the root filesystem ("/"). The -h option formats sizes in a human-readable format, and the --output=pcent option specifies that only the percentage of disk usage should be output.
- **|**: Pipe operator, redirects the output of the df command to the input of the awk command.

### awk 'NR==2{sub("%","",$1); print $1}': 
- Uses awk to process the second line (NR==2) of the df command output. It removes the "%" symbol from the first field ($1) using the sub() function, then prints the modified field. This field contains the disk usage percentage.

- **_disk_usage_percent=$(...)_**: Assigns the disk usage percentage to the variable disk_usage_percent.
- **_echo "$disk_usage_percent%"_**: This command echoes (prints) the disk usage percentage with a "%" symbol appended to it.
- **}**: Closing curly brace indicating the end of the function body.
------------------------
## Function to get detailed information about users logged in

    get_users_details() {

    users_details=$(who | awk '{print $1 " " $3 " " $4}')

    echo "$users_details"

    }
------------------------
- **_get_users_details()_**: This is a shell function declaration. It defines a function named get_users_details.
- **{**: Opening curly brace indicating the beginning of the function body.

### users_details=$(who | awk '{print $1 " " $3 " " $4}'): 
- This command collects detailed information about users who are currently logged in. Here's what it does:

- **_who_**: Runs the who command to display information about currently logged-in users.
- **|**: Pipe operator, redirects the output of the who command to the input of the awk command.
- **_awk '{print $1 " " $3 " " $4}'_**: Uses awk to process each line of the who command output. It prints the first field (username, $1), the third field (terminal or display where the user is logged in, $3), and the fourth field (time of login, $4), separated by spaces.
- **_users_details=$(...)_**: Assigns the collected user details to the variable users_details.
- **_echo "$users_details"_**: This command echoes (prints) the collected user details.
- **}**: Closing curly brace indicating the end of the function body.
------------------------
## Function to get number of users logged in

    get_users_logged_in() {

    users_logged_in=$(who | wc -l)

    echo "$users_logged_in"

    }
------------------------
- **_get_users_logged_in()_**: Shell function declaration. It defines a function named get_users_logged_in.
- **{**: Opening curly brace indicating the beginning of the function body.

### users_logged_in=$(who | wc -l): 
- This command counts the number of currently logged-in users. Here's what it does:

- **_who_**: Executes the who command to list the currently logged-in users.
- **|**: Pipe operator, redirects the output of the who command to the input of the wc command.
- **_wc -l_**: Counts the number of lines in the input.
- **_users_logged_in=$(...)_**: Assigns the count of logged-in users to the variable users_logged_in.
- **_echo "$users_logged_in"_**: This command echoes (prints) the count of logged-in users.
- **}**: Closing curly brace indicating the end of the function body.
------------------------
## Function to get top 10 CPU-consuming processes filtered by process name

    get_top_cpu_processes() {

    top_processes=$(top -bn1 | awk '/^ *[0-9]+/ {print $9,$12}' | sort -nr | head -n 10)

    echo "$top_processes"

    }
------------------------
- **_get_top_cpu_processes()_**: This is a shell function declaration. It defines a function named get_top_cpu_processes.
- **{** : Opening curly brace indicating the beginning of the function body.

### top_processes=$(top -bn1 | awk '/^ *[0-9]+/ {print $9,$12}' | sort -nr | head -n 10)
- This command retrieves information about the top 10 CPU-consuming processes. Here's what it does:

- **_top -bn1_**: Runs the top command in batch mode (-b) for one iteration only (-n1).
- **|**: Pipe operator, redirects the output of the top command to the input of the awk command.

### awk '/^ *[0-9]+/ {print $9,$12}':

- Uses awk to process lines starting with one or more spaces followed by digits (/^ *[0-9]+/). It prints the ninth and twelfth fields, which typically contain the CPU usage and process name.

- **|**: Pipe operator, redirects the output of the 'awk' command to the input of the 'sort' command.
- **_sort -nr_**: Sorts the input numerically (-n) in reverse order (-r). This sorts the CPU usage in descending order, ensuring the most CPU-consuming processes appear first.
- **|**: Pipe operator, redirects the output of the sort command to the input of the head command.
- **_head -n 10_**: Outputs the first 10 lines of the sorted output, which represent the top 10 CPU-consuming processes.
- **_top_processes=$(...)_**: Assigns the information about the top CPU-consuming processes to the variable top_processes.

- **_echo "$top_processes"_** : This command echoes (prints) the information about the top CPU-consuming processes.
- **}** : Closing curly brace indicating the end of the function body.
------------------------
## Function to write data to CSV file

write_to_csv() {

    local date_time cpu_usage memory_usage disk_usage users_logged_in top_cpu_processes users_details



    date_time=$(date "+%Y-%m-%d %H:%M")

    cpu_usage=$(get_cpu_usage)

    memory_usage=$(get_memory_usage)

    disk_usage=$(get_disk_usage)

    users_logged_in=$(get_users_logged_in)

    top_cpu_processes=$(get_top_cpu_processes)

    users_details=$(get_users_details)



    echo "Date and Time,CPU Usage,Memory Usage,Disk Usage,Users Logged In,User Details" >> system_metrics.csv

    echo "$date_time,$cpu_usage,$memory_usage,$disk_usage,$users_logged_in,$users_details" >> system_metrics.csv

    echo "Top 10 CPU-consuming processes:" >> system_metrics.csv

    echo "$top_cpu_processes" >> system_metrics.csv

    }

----------------------------
- local date_time cpu_usage memory_usage disk_usage users_logged_in top_cpu_processes users_details: This line declares local variables within the function scope. These variables will be used to store various system metrics.

- **_date_time=$(date "+%Y-%m-%d %H:%M")_**: This command retrieves the current date and time in the format "YYYY-MM-DD HH:MM" using the date command and assigns it to the variable date_time.

- cpu_usage=$(get_cpu_usage), memory_usage=$(get_memory_usage), disk_usage=$(get_disk_usage), users_logged_in=$(get_users_logged_in), top_cpu_processes=$(get_top_cpu_processes), users_details=$(get_users_details): These commands call respective functions to retrieve CPU usage, memory usage, disk usage, number of users logged in, top CPU-consuming processes, and user details, and assign the results to their respective variables.

- **_echo "Date and Time,CPU Usage,Memory Usage,Disk Usage,Users Logged In,User Details" >> system_metrics.csv_**: This command appends a header line to the system_metrics.csv file, specifying the names of the metrics being recorded.

- **_echo "$date_time,$cpu_usage,$memory_usage,$disk_usage,$users_logged_in,$users_details" >> system_metrics.csv_**: This command appends a line to the system_metrics.csv file containing the current date and time, CPU usage, memory usage, disk usage, number of users logged in, and user details, separated by commas.

- **_echo "Top 10 CPU-consuming processes:" >> system_metrics.csv_**: This command appends a line to the system_metrics.csv file indicating the start of the section containing the top 10 CPU-consuming processes.

- **_echo "$top_cpu_processes" >> system_metrics.csv_**: This command appends the information about the top CPU-consuming processes retrieved earlier to the system_metrics.csv file.
----------------------------
## Function to write data to HTML file

    write_to_html() {

    local date_time cpu_usage memory_usage disk_usage users_logged_in top_cpu_processes users_details



    date_time=$(date "+%Y-%m-%d %H:%M")

    cpu_usage=$(get_cpu_usage)

    memory_usage=$(get_memory_usage)

    disk_usage=$(get_disk_usage)

    users_logged_in=$(get_users_logged_in)

    top_cpu_processes=$(get_top_cpu_processes)

    users_details=$(get_users_details)



    echo "<html>

    <head>

        <title>System Metrics Report</title>

    </head>

    <body>

        <h1>System Metrics Report</h1>

        <p>Date and Time: $date_time</p>

        <p>CPU Usage: $cpu_usage</p>

        <p>Memory Usage: $memory_usage</p>

        <p>Disk Usage: $disk_usage</p>

        <p>Users Logged In: $users_logged_in</p>

        <p>User Details:</p>

        <pre>$users_details</pre>

        <h2>Top 10 CPU-consuming processes:</h2>

        <pre>$top_cpu_processes</pre>

    </body>

    </html>" > system_metrics.html

    }

----------------------------
- **_write_to_html()_**: Shell function declaration. It defines a function named "write_to_html".
- **{** : Opening curly brace indicating the beginning of the function body.
- local date_time cpu_usage memory_usage disk_usage users_logged_in top_cpu_processes users_details --> This line declares local variables within the function scope. These variables will be used to store various system metrics.

- date_time=$(date "+%Y-%m-%d %H:%M"),
- cpu_usage=$(get_cpu_usage),
- memory_usage=$(get_memory_usage),
- disk_usage=$(get_disk_usage),
- users_logged_in=$(get_users_logged_in),
- top_cpu_processes=$(get_top_cpu_processes),
- users_details=$(get_users_details):

These commands retrieve the current date and time, CPU usage, memory usage, disk usage, number of users logged in, top CPU-consuming processes, and user details, and assign them to their respective variables.

----------------------------

## Function to send email with HTML content (Insert your credentials!)

    send_email() {

    cat system_metrics.html | mail -s "System Metrics Report" -a "Content-Type: text/html" YOUR_MAIL_ADDRESS@DOMAIN.com

    }
----------------------------
- **_send_email()_**: This is a shell function declaration. It defines a function named send_email.
- **{** : Opening curly brace indicating the beginning of the function body.
- **_cat system_metrics.html_**: This command reads the contents of the system_metrics.html file.
- **|** : Pipe operator, redirects the output of the cat command to the input of the mail command.

### mail -s "System Metrics Report" -a "Content-Type: text/html" example09@gmail.com:

- This command sends an email with the contents of the system_metrics.html file as the body. Here's what each part does:
- **_-s "System Metrics Report"_**: Specifies the subject of the email as "System Metrics Report".
- **_-a "Content-Type: text/html"_**: Adds an attachment with the MIME type set to text/html, indicating that the email contains HTML content.
- **_example@gmail.com_**: Specifies the recipient email address.
- **}** : Closing curly brace indicating the end of the function body.
----------------------------

## Main function

    main() {

    echo "Collecting system metrics..."

    write_to_csv

    write_to_html

    send_email

    }



    # Run the main function

    main

----------------------------
- **_main()_**: Shell function declaration. It defines a function named main.
- **{** : Opening curly brace indicating the beginning of the function body.
- **_echo_** "Collecting system metrics...": This command displays the message "Collecting system metrics..." to indicate that the process of gathering system metrics is starting.
- **_write_to_csv_**: This command invokes the write_to_csv function, which collects system metrics and writes them to a CSV file.
- **_write_to_html_**: This command invokes the write_to_html function, which generates an HTML report containing system metrics and saves it to an HTML file.
- **_send_email_**: This command invokes the send_email function, which sends an email with the system metrics report attached as HTML content.
- **}** : Closing curly brace indicating the end of the function body.
- **_main_**: This line outside the function definition calls the main() function, initiating the execution of the script.
----------------------------


