# **bitmagnet Docker FAQ**

**Q1.** I have set category 'xxx' to be deleted by defining it in the config.yml under the section 'delete_content_types', but I still see the number of items going up. Why is this?

**A1.** If you 'Order By' 'Published', you will note that the 'Published' time will start to increase as new content is not added. The number of indexed items will increase, but no further items will be displayed for the category.

**Q2.** I have set category 'xxx' to be deleted by defining it in the config.yml under the section 'delete_content_types', but the number of items indexed in the category 'xxx' is not going down. Why is this?

**A2.** Items already indexed will not be retrospectively deleted. What the section name 'delete_content_types' is referring to is that new content added that matches the category specified will be auto-deleted.

**Q3.** I would like to interrogate the Postgres bitmagnet database using [pgAdmin](https://www.pgadmin.org/). How do I do this?

**A3.** In order to access the Postgres database, you have to perform 4 steps:

1. Ensure port 5432 is defined for the container. If you are using a VPN, then ensure it's defined in the value for the variable (env var) 'VPN_INPUT_PORTS'.
2. Install [pgAdmin](https://www.pgadmin.org/)—tested tooling, but you can use others if you wish.
3. Permit connection to Postgres by editing the file xxxx.
4. Permit connection to Postgres by editing the file xxxxx