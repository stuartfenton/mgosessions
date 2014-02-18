mgosessions
===============
An golang mgo (Mongodb) session store for martini. 

Since Martini uses Gorilla this store extends gorilla to create a sessions store using Mongo.

The code is pretty much unmodified from the original Gorilla code on another project but it was not packaged up so I packaged it up for martini.


##Usage

``` go
//I'm using a global to store the session database but you don't need to
var DBSESS *mgo.Session

func init() {

	//middleware

	//db handler
	m.Use(DB())

  //sessions
  store := mgosessions.NewMongoStore(DBSESS.DB(params["mdb"]).C("sessions"), []byte(params["secret"]))
  m.Use(sessions.Sessions("sessionid", store))
  
  //do a test
	//testSessionFlashes(t, store)

}

//
// MongoDb middleware -  Returns a martini.Handler
//
func DB() martini.Handler {
	//
	//global mongo database connection - used for sessions as well
	//
	var err error
	DBSESS, err = mgo.Dial(params["muri"])
	if err != nil {
		//panic(err)
		fmt.Println("MGO Dial: ", err)

	}

	return func(c martini.Context) {
		s := DBSESS.Clone()
		c.Map(s.DB(params["mdb"]))
		defer s.Close()
		c.Next()
	}
}

##In a function

//db would be the database
//and sess would be the mgo sessions tore
func PostAuth(req *http.Request, w http.ResponseWriter, db *mgo.Database, sess sessions.Session) (int, []byte) {

	v := session.Get("hello")
}

