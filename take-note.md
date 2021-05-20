1. Install mysql on ubuntu
sudo apt update
sudo apt install mysql-server
sudo mysql_secure_installation
Access from cmd: mysql -u root -p

Access from cmd to mssql server: sqlcmd -S localhost -U SA -P 'Your password'

https://gist.github.com/ethanclevenger91/8134ef434ec92d3d829be0b6beb714d9

# Download and extract phpmysql drivers
sudo wget "https://github.com/Microsoft/msphpsql/releases/download/v${PHP_MSSQL_RELEASE}/${PHP_MSSQL_DRIVERS}.tar" -O - | tar -x

# Change Directory
cd $PHP_MSSQL_DRIVERS

# Change owner/group
sudo chown root:root php_pdo_sqlsrv_74_nts.so
sudo chown root:root php_sqlsrv_74_nts.so
# Modify permissions
sudo chmod 644 php_pdo_sqlsrv_74_nts.so
sudo chmod 644 php_sqlsrv_74_nts.so

# Move files
sudo mv php_pdo_sqlsrv_74_nts.so /usr/lib/php/20190902
sudo mv php_sqlsrv_74_nts.so /usr/lib/php/20190902

# Cleanup
cd .. && sudo rm -rf $PHP_MSSQL_DRIVERS

# Create new .ini file
sudo touch /etc/php/7.4/mods-available/pdo_sqlsrv.ini
echo "extension=php_pdo_sqlsrv_74_nts.so" | sudo tee -a /etc/php/7.4/mods-available/pdo_sqlsrv.ini > /dev/null

# Create Symlinks
sudo ln -sfn /etc/php/7.4/mods-available/pdo_sqlsrv.ini /etc/php/7.4/cli/conf.d/20-pdo_sqlsrv.ini
sudo ln -sfn /etc/php/7.4/mods-available/pdo_sqlsrv.ini /etc/php/7.4/fpm/conf.d/20-pdo_sqlsrv.ini

# Enable PHP extension
echo "extension=php_sqlsrv_74_nts.so" | sudo tee -a /etc/php/7.4/cli/php.ini > /dev/null
echo "extension=php_sqlsrv_74_nts.so" | sudo tee -a /etc/php/7.4/fpm/php.ini > /dev/null

# Install Microsoft ODBC Driver 17 for SQL Server requirements
curl https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
sudo sh -c 'curl https://packages.microsoft.com/config/ubuntu/20.04/prod.list > /etc/apt/sources.list.d/mssql-release.list'
# Update package lists
sudo apt-get update
# Install Microsoft ODBC Driver
yes Y | sudo ACCEPT_EULA=Y apt-get install msodbcsql17

# Restart services
sudo service php7.4-fpm restart
sudo service nginx restart
