# charts.js-with-dynamic-data
Hi, my name is André and this is a dev guide to use charts.js with dinamic data retrieved from tables and databases.  
For this project to work I needed:
 - Bootsrap: [https://getbootstrap.com/](https://getbootstrap.com/)
 - Jquery: [https://jquery.com/](https://jquery.com/)
 - Charts.js [https://www.chartjs.org/](https://www.chartjs.org/)

This will be my first git repository, just here to detail what I've done and contribute.

## Charts.js with dynamic data from MySQL databases' tables

Probably not the best way, but only one I found to be effective when dealing with Charts.Js and dynamic data.
For this I used PHP 7.3.1 and MySQL5.7.24

Lets start with database connection:

```
<?php
	$database_username = "username";
	$database_password = "password";
	$database_info = "mysql:host=localhost;dbname=databasename";

	//this will help you debug some query errors.
	$degub = true;
	$log = true;

	try	{
		$conn = new PDO($database_info, $database_username, $database_password);
		$conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
	} catch(PDOException $e) {
		// deal with exception here
	}
?>
```
This will connect your project with the database via PDO.  
Now the row counting function:
```
function count_table_rows($table, $conditions = null, $order = null) {
	try {
		global $conn;
		$query = "SELECT * FROM " . $table . " " . $conditions . " " . $order . ";";
		$result = $conn->query($query);
		$rowcount = $result->rowCount();
		return $rowcount;
	} catch (PDOException $e) {
		//deal with exception here
  	}
}
```
This function will allow you to quickly count the number os rows in a table with your custom conditions.
Lets say I have a table, Users, with 3 records.
| ID |Name   |  Email | Gender | Date of Birth | Password |
|---|---|---|---|---|---|
| 1 |Benecditc C|cumberbitch@strange.com|Male|1976/07/19|$2y$10$eMrcdQlFxQ2.f8Ni.w5TA|
| 2 |Robert D Jr|rdjr@luv3000.com|Male|1965/04/04|$2y$10$dfsdsfsFxQ2.f8Ni.gfdd|
| 3 |Cate B|cate@hela.com|Female|1969/05/14|$2y$10$TgjfgfsFxQ2.f8Ni.WFGd|

Now, I want to count how many female users there are:
```
count_table_rows("users", "WHERE gender LIKE 'female'") 
//Since I don't need to order, no need to use it.
```
The output will be 1, since there's only one Female listed.
## Charts.php
What comes next may not be the best way to approach it, I personaly found it to be effecient.
I've created a file ```chats.php```, where I put every single function required for the charts.js to work.  
Consider the next table.  
|ID|Name|Email|Gender|Date of Birth|Occupation|
|---|---|---|---|---|---|
|1|Benecditc C|cumberbitch@strange.com|Male|1976/07/19|Actor|
|2|Robert D Jr|rdjr@luv3000.com|Male|1965/04/04|Actor|
|3|Cate B|cate@hela.com|Female|1969/05/14|Unemployed| 
 
I can now run a script in the  ```charts.php``` like so:
```
$occupation = array (
	"actor" => count_table_rows("user", "WHERE occupation = 'Actor'"),
	"unemployed" => count_table_rows("user", "WHERE occupation = 'Unemployed'")
);
```
This will create an array with the exact number of actor and/or unemployed registries.  
This peace of code will need to be before your ```</head>``` tag in the file where you need the charts to work.  
```
<script>
	var occupation = <?=json_encode($occupation); ?>;
</script>
```
This will encode all the information from the database table to a json object.
## The pie chart
The next part is where you need to be careful with the changes you make. After installing the charts.js plugin, go the folder where you have the javascript for the chart you need. It'll look something like this:
```
// Pie Chart Example
var ctx = document.getElementById("occupation");
var myPieChart = new Chart(ctx, {
	type: 'doughnut',
	data: {
		labels: ["Actor", "Unemployed"],
		datasets: [{
			data: [occupation.actor, occupation.unemployed],
			backgroundColor: ['#4e73df','#36b9cc'],
			hoverBorderColor: "rgba(234, 236, 244, 1)",
		}],
	},
	options: {
		maintainAspectRatio: true,
		tooltips: {
			backgroundColor: "rgb(255,255,255)",
			bodyFontColor: "#858796",
			borderColor: '#dddfeb',
			borderWidth: 1,
			xPadding: 15,
			yPadding: 15,
			displayColors: false,
			caretPadding: 10,
		},
		legend: {
			display: false
		},
		cutoutPercentage: 80,
	},
});
```
Change the lines ```labels:``` & ```backgroundColor:``` to whatever you want and in the line ```data:```
you will call the json object like so: ```[occupation.actor, occupation.unemployed]```. This will access the json object and return the values that were first encoded there.
## Conclusion
I had some fun trying to figure out how to connect the database and the charts.js togheter for a project I was developing, and learned some new stuff.  
This will be my MO, at least until I figure out another way to do it, a simpler way...