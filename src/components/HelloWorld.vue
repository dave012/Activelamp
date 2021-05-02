<template>
  <div class="col-6 flex justify-center ml-auto mr-auto">
    <form class="block left-align">
      <label>SITE:</label>
      <input id="site" type="url" name="site">
      <button type="button" @click="generateURL()">Submit</button>
      <br>
      <br>
      <span>Shorten URL: <a target="_blank" :href="inputSite">{{generatedUrl}}</a></span>
    </form>
  </div>
</template>

<script>
export default {
  name: '',
  props: {
  },
  data () {
    return {
      dbObj: '',
      generatedUrl: '',
      generatedString: '',
      inputSite: ''
    }
  },
  created () {
    var Database_Name = 'MyDatabase';  
    var Version = 1.0;  
    var Text_Description = 'My First Web-SQL Example';  
    var Database_Size = 2 * 1024 * 1024;  
    this.dbObj = openDatabase(Database_Name, Version, Text_Description, Database_Size);
    this.createDB();
    if (window.location.pathname.length > 1) {
      this.generatedUrl = window.location.href;
      this.redirect()
    }
  },
  methods: {
    createDB () {
      var obj = this.dbObj
      obj.transaction(function (tx)  
      {
        tx.executeSql('CREATE TABLE IF NOT EXISTS url_table (url, site)');
      });
    },
    generateURL () {
      let generate = Math.random().toString(36).substring(7);
      this.generatedString = generate;
      this.insertData(generate);
    },
    insertData (generate) {
      var site = document.getElementById("site").value;
      this.inputSite = site;
      var url = 'http://activelamp.local:8080/' + generate;
      var obj = this.dbObj
      if (site != '') {
        this.generatedUrl = url;
        obj.transaction(function (tx) {  
          tx.executeSql('insert into url_table (url, site) values("' + url + '", "' + site + '")');
        });
      } else {
        alert('Enter site url');
      }
    },
    redirect () {
      var obj = this.dbObj;
      var generated = this.generatedUrl
      obj.transaction(function (tx) {  
        tx.executeSql('SELECT * from url_table where url="' + generated + '"', [], function (tx, results) {
          window.location.replace(results.rows.item(0).site);
        });
      });  
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.left-align {
  text-align: left;
}
.ml-auto {
  margin-left: auto;
}
.mr-auto {
  margin-right: auto;
}
.col-6 {
  width: 50%;
}
.block {
  display: block;
}
.flex {
  display: flex;
}
.justify-center {
  justify-content: center;
}
</style>
