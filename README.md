# Realm Task

This is the simplest method of connecting to Atlas via realm and displaying data.  Its one html file.  Simply replace the application id (YOUR_APP_ID) with your task application id in the html file.   

Next steps:   
1. Allow anonymous authentication   
2. Set up rules allowing read access to tracker.task   


```html
<!DOCTYPE html>
<html lang="en">
<head>
  <!--
  ==============================================================================================================================
  - Date:       Author:           Version:     Notes:
  ------------------------------------------------------------------------------------------------------------------------------
  - 2021-03-15  Britton LaRoche   1.0          First Version
  -
  ==============================================================================================================================
  -->
  <title>Simple web page for task tracker</title>
  <script src="https://unpkg.com/realm-web@0.9.0/dist/bundle.iife.js"></script>
  <script type="text/javascript">
  const appId = "YOUR_APP_ID"; // Set Realm app ID here.
  let user;
  let app;
  let credentials;
  let mongo;
  let items;
  const appConfig = {
    id: appId,
    timeout: 1000,
  };
  async function init(){
    try{
      await atlasConnect();
    } finally {
      console.log("Init Complete");
    }
  }
  async function atlasConnect(){
      app = new Realm.App(appConfig);
      credentials = Realm.Credentials.anonymous(); // create an anonymous credential
      user = await app.logIn(credentials);
      console.dir(user);
      mongo = app.services.mongodb("mongodb-atlas");
  }
  async function loadDbData(){
    var destDatabase = "tracker";
    var destCollection = "Task";
    const db = mongo.db(destDatabase);
    const coll = db.collection(destCollection);
    items = await coll.find({});
    console.log("Items Length: " + items.length);
    console.dir(items);
    var numDocs = items.length;
    var tableHtml = "<table border=1><th>NAME</th><th>STATUS</th>";
    for  (i=0;i<numDocs;i++){
        tableHtml = tableHtml +"<tr><td>" + items[i].name + "</td><td>" + items[i].status + "</td></tr>" ;
    }
    tableHtml = tableHtml + "</table>";
    document.getElementById("recordsLoaded").innerHTML = "Documents loaded from database: " + numDocs;
    document.getElementById("model-status").innerHTML = `Data loaded from database.`;
    document.getElementById("tasks").innerHTML = tableHtml;
  }
    init();
</script>
</head>
<body>
    <button type="submit" id="refresh" onClick="loadDbData()">Load Data</button>
    <div id="recordsLoaded"></div>
    <div id="model-status"></div>
    <hr>
    <div id="tasks"></div>
</body>
</html>
```
