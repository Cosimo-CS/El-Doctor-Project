Full script here


´´´bash

    #!/bin/bash



    # Function to collect CPU usage

    get_cpu_usage() {

    cpu_percent=$(top -bn1 | awk '/Cpu\(s\)/{print $2}')

    cpu_usage=$(printf "%.0f" "$cpu_percent")

    echo "$cpu_usage%"

}



    # Function to collect memory usage

    get_memory_usage() {

    memory_percent=$(free -m | awk 'NR==2{print $3*100/$2}')

    echo "$memory_percent%"

    }



    # Function to collect disk usage

    get_disk_usage() {

    disk_usage_percent=$(df -h --output=pcent / | awk 'NR==2{sub("%","",$1); print $1}')

    echo "$disk_usage_percent%"

    }



    # Function to get detailed information about users logged in

    get_users_details() {

    users_details=$(who | awk '{print $1 " " $3 " " $4}')

    echo "$users_details"

    }



    # Function to get number of users logged in

    get_users_logged_in() {

    users_logged_in=$(who | wc -l)

    echo "$users_logged_in"

    }



    # Function to get top 10 CPU-consuming processes filtered by process name

    get_top_cpu_processes() {

    top_processes=$(top -bn1 | awk '/^ *[0-9]+/ {print $9,$12}' | sort -nr | head -n 10)

    echo "$top_processes"

    }



    # Function to write data to CSV file

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



    # Function to write data to HTML file

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



    # Function to send email with HTML content

    send_email() {

    cat system_metrics.html | mail -s "System Metrics Report" -a "Content-Type: text/html" YOUR-EMAIL-ADDRESS@DOMAIN.com

    }



    # Main function

    main() {

    echo "Collecting system metrics..."

    write_to_csv

    write_to_html

    send_email

    }



    # Run the main function

    main





´´´
