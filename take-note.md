1. Install mysql on ubuntu
sudo apt update
sudo apt install mysql-server
sudo mysql_secure_installation
Access from cmd: mysql -u root -p

Access from cmd to mssql server: sqlcmd -S localhost -U SA -P 'Your password'
