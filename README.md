# sortingvisualizer.github.io
This is My Sorting Visualizer Website.

project Link : https://surajvs.github.io/sortingvisualizer.github.io/


**index.js --
const express = require("express"); // importing express package
const cors = require("cors"); // cross origin resource sharing 
const app = express();  //instance to use all tha methods of express
const PORT = process.env.PORT || 5000;// port number
const { Sequelize } = require('sequelize'); //doubt  // importing sequelize
const  BodyParser=require ("body-parser"); //middleware - as a work as a middle layer between req and res;
//middleware example
//  app.get("/",middleware,(req,res)=>{
// res.status(201).json("hello")
//  }
// //  )

// here middlware is a function which use to check use is  fullfilling  the condition or not . if yes then got to next else loading...


require("./db/conn");
const routera = require("./router/auth");
const routerb = require("./router/userapi");
const { condition } = require("sequelize");






// const corsOptions={
//     origin:`http://localhost:8081`,
// };

app.use(cors());
app.use(BodyParser.json({extended : true}));
app.use(BodyParser.urlencoded({extended : true})); //doubt




app.use(routera);
app.use(routerb);

//routes...........................


//localhost...............................................................
app.listen(PORT,()=>{
    console.log(`server is running on ${PORT}`)
}) 




**conn.js ----

const { Sequelize, DataTypes } = require("sequelize");
const sequelize = new Sequelize("userdata", "root", "root", {
    host: "localhost",
    dialect: "mysql",
    operatorsAliases: false,
    pool: {
        max: 5,
        min: 0,
        acquire: 30000,
        idle: 10000
    }
}
);
sequelize.authenticate()
.then(() => {
    console.log('connected..');
}).catch(err => {
    console.log('Error : '+ err);
});

const db = {};

db.Sequelize = Sequelize;
db.sequelize = sequelize;

db.User = require("../models/userschema")(sequelize, DataTypes);
db.Auth = require("../models/authschema")(sequelize, DataTypes);

db.sequelize.sync({force:false}).then((result)=>{
    console.log("sync is done");
}).catch((error)=>{
    console.log(error);
});
module.exports = db;



model ====

 

module.exports  = (sequelize, DataTypes)=>{
const User = sequelize.define("user", {
   id: {
      type: DataTypes.INTEGER,
      allowNull: false,
      autoIncrement: true,
      primaryKey: true
   },
   name:{
      type: DataTypes.STRING,
      allowNull: false
  },
  email:{
      type: DataTypes.STRING,
      allowNull: false
  }
});
return User;
}




*router ====


const express = require("express");
const router = express.Router();

const db = require("../db/conn");
const User = db.User;

router.post("/adddata", async(req, res) => {
    const { name, email } = req.body;
    try {
        let info = {
            name: name,
            email: email
        };
        const data = await User.create(info);
        if (data) {
            res.status(200).send(data);
        }
    } catch (error) {
        res.status(422).send(error);
    }
});

router.get("/getalldata", async(req, res) => {
    try {
        const data = await User.findAll({});
        if (data) {
            res.status(200).send(data);
        }
    } catch (error) {
        res.status(422).send(error);
    }
    
});

router.get("/getsingledata/:id", async(req, res) => {
    try {
        let id = req.params.id;
        const data = await User.findOne({where :{id:id}});
        if (data) {
            res.status(200).send(data);
        }
    } catch (error) {
        res.status(422).send(error);
    }
    
});

router.delete("/deletesingledata/:id", async(req, res) => {
    try {
        let id = req.params.id;
        const data = await User.destroy({where :{id:id}});
        if (data) {
            res.status(200).send("data is deleted");
        }
    } catch (error) {
        res.status(422).send(error);
    }
    
});
router.put("/updatedata/:id", async(req, res) => {
    try {
        let id = req.params.id;
        const data = await User.update(req.body,{where :{id:id}});
        if (data) {
            res.status(200).send("data is update");
        }
    } catch (error) {
        res.status(422).send(error);
    }
    
});
module.exports = router;
