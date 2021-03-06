# Fizzit.com - An Integrated group project 


**Table Of Contents**

- [Getting Started](#getting-started)
    - [Requirements](#requirements)
    - [Quick start](#quick-start)
    - [Manual install](#manual-install)
- [Project Brief](#project-brief)
    - [Introduction](#introduction)
    - [Making a small profit](#making-a-small-profit)
    - [Detailed customer process on Fizzit](#detailed-customer-process-on-fizzit)
    - [Key functional requirements for conducting a trade](#key-functional-requirements-for-conducting-a-trade)
    - [Project goal](#project-goal)
    

## Getting Started

### Requirements 
1. JDK8 - Not a minimum JDK, Glassfish must be run with JDK8 only.
2. Maven 

### Quick start 
In the project directory a shell script has been made to make it easy to download the required software and
configure the server then deploy Fizzit onto that server. 

To get started do the following: 

``` console 
foo@bar:~$ git clone https://github.com/DA-Wishart/Fizzit.com.git
foo@bar:~$ cd Fizzit.com
```

In this directory the shell script is called fizzit.sh, depending on your settings
you may need to change permissions in order to allow the execution of this script. 

You will also need to add your database details to the top of the script and save it. 
The top of the script will have the following lines that need to be changed: 

DBNAME=DATABASE_NAME
DBUSER=DATABASE_USER
DBPASS=DATABASE_PASSWORD
DBURL=DATABASE_URL
DBPORT=DATABASE_PORT

Change the left hand values with your database information and then the script will be ready to run. 

*Tutors moderating this project can find the database information in the group report appendix.*


``` console
foo@bar:~/Fizzit.com$ ./fizzit.sh 
```

Running this script will go through all the steps of the manual installation. 
Simply the Glassfish server will be downloaded and configured and the Fizzit.com 
project will be compiled with the WAR file moved to the Glassfish server ready for
when the server is started.

### Manual install

1. Install Glassfish server version 5.1 
2. Download the mysql-connector-java-8.0.15.jar ^
    1. Place the jar in the server library - glassfish5/glassfish/domains/domain1/lib/
3. Create a new connection pool on Glassfish server 
This step requires starting the Glassfish server which can be done using the following command:

    ``` console 
    foo@bar:PATH_TO_GLASSFISH/glassfish5/bin$ ./asadmin start-domain
    ```

    To stop the Glassfish server then use the following:

    ``` console 
    foo@bar:PATH_TO_GLASSFISH/glassfish5/bin$ ./asadmin stop-domain
    ```

    There are two ways to create a new connection pool - through the GUI or through CLI. 

    In this section we only cover the CLI as with the commands to follow it will be easier 
    and quicker then the GUI. The following command creates the a new connection pool:

    ``` console 
    foo@bar:PATH_TO_GLASSFISH/glassfish5/bin$ ./asadmin create-jdbc-connection-pool --ping 
    --restype javax.sql.DataSource --datasourceclassname com.mysql.cj.jdbc.MySqlDataSource --property 
    user=$DBUSER:password=$DBPASS:DatabaseName=$DBNAME:ServerName=$DBURL:port=$DBPORT:useSSL=false 
    fizzit_pool 
    ```

    Replace each occurrence of $XXX with your specific database information - Fizzit.com 
    is configure to work with a MySql database so using any other vendor would require changes 
    within the project to support (persistence.xml for those brave enough). 

    **NB: Glassfish server needs to be running in order to create a new connection pool**

4. Configure Glassfish to use the new connection pool by default 

    This can be achieved through the GUI or CLI as before with the connection pool. 

    The following command can be used and doesn't require any changes: 

    ``` console 
    foo@bar:PATH_TO_GLASSFISH/glassfish5/bin$ ./asadmin set 
    resources.jdbc-resource.jdbc/__default.pool-name=fizzit_pool
    ```
    
    **NB: Glassfish server needs to be running in order to configure JDBC resources**

    Glassfish is now ready to deploy the Fizzit WAR file.

5. Clone the project repository 

    ``` console 
    foo@bar:~$ git clone https://github.com/DA-Wishart/Fizzit.com.git
    ```

6. Compiling the project to a WAR file

    Compiling the project is taken care of by Maven so this step is a single command

    ``` console 
    foo@bar:PROJECT_DIRECTORY$ mvn clean package 
    ```

7. Move the compiled WAR file into Glassfish

    ``` console
    foo@bar:PROJECT_DIRECTORY$ cp ./target/Fizzit.com.war 
    PATH_TO_GLASSFISH/glassfish5/glassfish/domains/domain1/autodeploy
    ```

    If the server is running then Fizzit.com should become deployed and viewable at 
    http://localhost:8080/Fizzit.com
    If the server is not running then Fizzit will be deployed once you start up the server. 


^ *The project is based on the assertion of using MySql so in order to use a different vendor 
more configuration changes would be required.*

## Project Brief

### Introduction
Selling your stuff has never been easier. Fizzit.com is an online company that sets its vision as
“To Be the UK's Easiest Online trader of Books, CDs and DVDs”. It has a number of sub-goals
to help achieve this vision. Fizzit’s aim is to create a strong value proposition for its customers
by ensuring that its customers don't have to do much at all to trade their unwanted products.
Fizzit wants to ensure customers get a reasonable price for the products. Fizzit wants to make
sure it can also trade on (read: sell) whatever it buys at a reasonable price also; covering its
operational costs is vital but also making a small profit.
Fizzit simply asks its customers to enter the barcode/ISBN of a product (CD, DVD, Book,
Game) into its website. The Fizzit solution should be able to access that product record,
return to the customer a price it is willing to pay for the product or whether it does not want
the product. This Fizzit calls the ‘one input price return’ goal. Once the Customer has either 10
products entered or a total trade value of over £10, then a trade can proceed. The products
are simply delivered to Fizzit who then pay the customer.

### Making a small profit
The management of Fizzit plan to take any products traded and on-sell them via a different
company (a partner company but an entirely independent legal entity). The World Of Online
(TWOO) is an online catalogue akin to Amazon.co.uk but only dealing in second-hand i.e. used
products. TWOO trades in 30 countries and its biggest customer base outside of the UK is
both Australia and China. What sells quickly (within 48 hours of going live in the TWOO
catalogue) is fed back to the Fizzit database so that it can either update prices or inform
customers what it will or will not now trade. Fizzit wants to create a product supply chain for
TWOO and it does this by stocking its warehouse on a weekly basis with products it collects
from couriers and drop off locations. The hope is TWOO will provide a return value to Fizzit
through rapid feedback on what sells and what mark-up Fizzit may be able to make.

### Detailed Customer Process on Fizzit
Fizzit works like this: a customer logs in or registers to create an account if a new customer.
Immediately the customer is presented with an input box prompting the customer to enter a
barcode/ISBN of a product to sell. When a price is returned, the customer moves the item to
a 'trade account' (or 'save for later') and executes the trade. The customer can either take the
product (assuming 10 items or a trade value of £10 has been reached) to a delivery drop-off
box (there are 10,000 in the UK housed mostly in newsagents / convenience stores) or if it is a
very large item (over 25kg and with a trade value of over £25) a courier service will be
despatched to pick it up from the customer's address.
Products are collected and centrally stored in a warehouse in southern England once a week.
Here they are checked for quality within 24 hours of arrival. Assuming good quality, the
customer's PayPal account or bank account is credited with the amount of the trade within a
further 24 hour period. The customer is informed via an email from Fizzit of the successful
trade. There is a no returns policy once the trade is complete. If upon inspection in the
warehouse some or all goods are in such disrepair that they cannot be sold easily – in the
opinion of Fizzit – then no payment is made to the customer for these specific products; the 
unsellable products are placed in the nearest drop-off box to the customer's postal address
and an email sent with information about the product rejection and how to pick up the goods.
If the product is large the goods are returned via courier. The customer is not charged for the
return but is given notice that if goods are found to be not good enough twice more in a
calendar year then they will be barred from selling via the site for a calendar year.

### Key functional requirements for conducting a trade
1. The customer must be able to enter a barcode number or scan it in
2. The customer can enter up to 100 product barcodes per trade
3. The minimum number of products per trade is 10
4. When the customer enters a barcode, the Fizzit system will display only 1 of 2
messages:
    1. Return the price Fizzit is willing to pay the customer for the product (book, CD,
    DVD, game) and a description of the product (product title, author (if
    applicable), publisher, year published, brief description)
    2. If the book is not required by TWOO a message will be displayed stating the
    book is not required by Fizzit currently.
5. The customer can see the entered products for trade in a table and the price offered
for sale, including a total price offered.
6. The customer can cancel the trade of any entered item by either:
    1. Selecting ‘Save for later’ which moves the product out of the ‘basket’ and into
    a holding account
    2. Selecting ‘Delete item entirely’ which will remove the entry from the Fizzit
    system
7. If the customer wants to trade a product from the ‘Save for later’ list, he can select
the ‘Trade now’ option which moves it back into the trade account.
8. Upon selecting: ‘Conduct trade’ the products on the screen are moved to a ‘Traded’
account. It has a status pending until the approval for payment is activated.
9. The customer can view the ‘Pending trade’ list but cannot ‘Save for later’. However,
the entire trade can be cancelled at any point prior to the products being sent for
collection by Fizzit.
10. Once a trade has been approved and products approved, the customer is notified of
its success and payment is authorised.
11. The customer can view a ‘Completed Trades’ table that lists all the successful trades
conducted by that customer.
12. In order to complete a trade, the Customer has to select drop off location, which is
chosen from a list of nearest stores to the customer’s postcode.
13. Upon selection of drop-off store, the customer finalises and approves the trade.
14. Upon selecting: ‘Trade’, the customer receives an email from Fizzit that includes
details of the trade and a cover page document for printing and placing on the
package that is taken to the drop-off location. It includes instructions for usage.

### Project Goal
Despite clear cut function requirements, there is no consideration of security or privacy. As
such, Fizzit would be in breach of GDPR and common sense security measures. It is your task
to document a set of security and privacy requirements that meet these functional requirements.
It is further your task to also implement 50% of these requirements (the ones you determine
are must haves) with their corresponding security and privacy requirements in tow. No
particular software/platform/OS required but you are anticipated to demonstrate
implementation of some part of the requirements as a prototype or proof of concept. 
