# charts.js-with-dynamic-data
Charts.js with dynamic data from MySQL databases' tables

Probably not the best way, but only one I found to be effective when dealing with Charts.Js and dynamic data.
For this I used PHP 7.3.1 and MySQL5.7.24

Lets start with some functions.

function count_table_rows($table, $conditions = null, $order = null){
    try {
			  global $conn;
			  $query = "SELECT * FROM " . $table . " " . $conditions . " " . $order . ";";
			  $result = $conn->query($query);
			  $rowcount = $result->rowCount();
			  return $rowcount;
		} catch (PDOException $e) {
			  return;
		}
}

This function will allow you to quickly count the number os rows in a table with your custom conditions.
Lets say I have a table, Users, with 3 records.
+---------------------+-------------------------+-------------------+-----------------------------+-------------------------------+
| Name                | Email                   | Gender            | Date of Birth               | Password                      |
+---------------------+-------------------------+-------------------+-----------------------------+-------------------------------+
| Benecditc C.        | cumberbitch@strange.com | Male              | 1976/07/19                  | $2y$10$eMrcdQlFxQ2.f8Ni.w5TA  |
+---------------------+-------------------------+-------------------+-----------------------------+-------------------------------+
| Robert D. Jr        | rdjr@luv3000.com        | Male              | 1965/04/04                  | $2y$10$dfsdsfsFxQ2.f8Ni.gfdd  |
+---------------------+-------------------------+-------------------+-----------------------------+-------------------------------+
| Cate Blanchett      | cate@hela.com           | Female            | 1969/05/14                  | $2y$10$TgjfgfsFxQ2.f8Ni.WFGd  |
+---------------------------------------------------------------------------------------------------------------------------------+

Now, I wanna count how many female users there are.
    count_table_rows("users", "WHERE gender LIKE 'female'") //Since I don't need to order, no need to use it.
The output will be 1.
