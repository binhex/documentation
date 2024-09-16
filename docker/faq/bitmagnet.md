# **bitmagnet Docker FAQ**

**Q1.** I have set category 'xxx' to be deleted by defining it in the config.yml under section 'delete_content_types' but i still see the number of items going up, why is this?

**A1.** Due to the slight delay between items being added to the Postgres database and items showing up in the Web UI you will see the number of items indexed go up for a short period of time (around 10-30 mins), but if you 'Order By' 'Published' you will note that the 'Published' time will start to increase as new content is not added.

**Q2.** I have set category 'xxx' to be deleted by defining it in the config.yml under section 'delete_content_types' but the number of items indexed in the category 'xxx' is not going down, why is this?.

**A2.** Items already indexed will not be retrospecively deleted, what the section name of 'delete_content_types' is referring to is new content added that matches the category specified will be auto deleted.
