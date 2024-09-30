# **bitmagnet Docker FAQ**

**Q1.** I have set category 'xxx' to be deleted by defining it in the config.yml under section 'delete_content_types' but i still see the number of items going up, why is this?

**A1.** If you 'Order By' 'Published' you will note that the 'Published' time will start to increase as new content is not added, the number of indexed items will increase but no further items will be displayed for the category.

**Q2.** I have set category 'xxx' to be deleted by defining it in the config.yml under section 'delete_content_types' but the number of items indexed in the category 'xxx' is not going down, why is this?.

**A2.** Items already indexed will not be retrospecively deleted, what the section name of 'delete_content_types' is referring to is new content added that matches the category specified will be auto deleted.

**Q3.** I would like to interrogate the PostGres bitmagnet database using [pgAdmin](https://www.pgadmin.org/) how do i do this?.

**A4.** In order to access the PostGres database you have to perform 4 steps:

1. Ensure port 5432 is defined for the container, if you are sing a vpn then ensure its defied in the value for variable (env var) 'VPN_INPUT_PORTS'
2. Install [pgAdmin](https://www.pgadmin.org/) - tested tooling but you can use others if you wish.
3. Permit connect to PostGres by editign the file xxxx
4. Permit connection to PostGres by editing the file xxxxx
4.