If you're following along, you should have surmised that API resources are ultimately backed by database entities. The relationship between API resources and database tables is intentionally direct and simple. Tables map pretty closely 1:1 to API resources. 

The notable caveat to the above rule is that if Resource Bar extends from Resource Foo, then they will both use the foo database table.

This article: [Model: Create Database Type](https://github.com/rancherio/rancher/wiki/Model:-Create-Database-Type).

See if you can follow that article to create a new resource called ```Animal```. Get to the point where you can CRUD Animals in the API.