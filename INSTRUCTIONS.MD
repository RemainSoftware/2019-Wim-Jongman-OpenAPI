This sample code is provided by IBM for illustrative purposes only. It has not been fully tested. 
It is provided as-is without any warranties of any kind, including but not limited to the implied 
warranties of merchantability and fitness for a particular purpose.

## Use RPG as CGI
This program is a simple RPG program that demonstrates the HTTP server APIs for reading standard input, 
reading an environment variable and writing standard output. This is done using the HTTP Server APIs.

## Installation
To install the example program, do the following:

### Download
Download the following files contained in this repository: 
* samplerpg.txt - contains the RPG source code.
* samplerpg.htlp - contains the html code that makes the request for the CGI.

### Create Directory Structure
Use the following CL commands to create a directory structure: 
* mkdir dir('/qsys.lib/rpgdemo.lib')
* mkdir dir('/qsys.lib/rpgdemo.lib/qcsrc.file')
* mkdir dir('/rpgdemo')
* mkdir dir('/rpgdemo/html')

  Be aware the mkdir command will use  hierarchical file authorities, unless you specify otherwise, 
  for the *PUBLIC users.  You may want to specifically add read and execute authorities for server 
  user profiles QTMHHTTP and QTMHHTP1 to these new directories.

### Upload
Use FTP to connect to your server.
Use the following FTP commands to copy the files to your server: 

* put samplerpg.html /rpgdemo/html/samplerpg.html
* put samplerpg.txt  /qsys.lib/rpgdemo.lib/qcsrc.file/samplerpg.mbr

### Compile
Use the following CL commands to create the program (PGM) object called SAMPLERPG: 

* CRTRPGMOD MODULE(rpgdemo/samplerpg) SRCFILE(rpgdemo/qcsrc) SRCMBR(samplerpg) OUTPUT(*print)
* CRTPGM PGM(rpgdemo/samplerpg) MODULE(rpgdemo/samplerpg) BNDSRVPGM(qhttpsvr/qzhbcgi)

### Setup HTTP Server
Use your browser to access the HTTP Server Configuration and Administration forms to create an Apache server named, 
for example, RPGCONFIG.
        
* Set the document root to the /rpgdemo/html directory. 
* Add a directory for the QSYS library allowing access to the directory and allowing CGI script execution, 
using the Alias and Redirection form. 
* Add the directory for the QSYS (RPGDEMO.LIB) library, using the Context Managementform.

### Check
Under Tools, select Display Configuration File to make sure you have the following CGI related directives:

        DocumentRoot /rpgdemo/html/
        Alias /html/ /rpgdemo/html/
        ScriptAlias /cgi-bin/ /QSYS.LIB/RPGDEMO.LIB/
        <Directory /rpgdemo/html/>
            AllowOverride None
            Options None
            order allow,deny
            allow from all
        </Directory>
        <Directory /QSYS.LIB/RPGDEMO.LIB/>
            AllowOverride None
            SetHandler cgi-script
            Options +ExecCGI
            order allow,deny
            allow from all
        </Directory>

### Start the Server
Start the RPGCONFIG instance from the GUI.

### Run
* From your browser type: http://yourservername:port/html/samplerpg.html .

The input for this program comes from CGI standard input or the environment variable QUERY_STRING.
For an HTTP request method of POST, the input is read from standard input and for an HTTP request 
method of GET, the input is read from QUERY_STRING. For method POST, this program will only read 1024 
characters. For method GET, the program will not read any input data when it exceeds 1024 characters.

The program reads data from standard input based on the Content_Length environment variable. 
The QtmhGetEnv System API is used to get the Content_Length and set the InDataLn variable used 
by the QtmhRdStdIn API. The data to be returned to the client is written to standard output using the 
QtmhWrStOut API. The data is returned as text/html.
