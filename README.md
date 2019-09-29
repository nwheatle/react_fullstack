# react_fullstack

steps to run the code

 1. cd backend
 2. npm install
 3. cd ..
 4. cd client
 5. npm install
 6. cd ..
 7. npm install
 7. npm start
  
 client/src/App.js was modified to use React Functions with Hooks, instead of React Class Components.

```
import React, { useState, useEffect } from "react";
import axios from "axios";


const App = () => {

  const [dataState, setDataState] = useState([]); 
  const [message, setMessage] = useState(null);
  const [intervalIsSet, setIntervalIsSet] = useState(false);
  const [idToDelete, setIdToDelete] = useState(null);
  const [idToUpdate, setIdToUpdate] = useState(null);
  const [updateToApply, setUpdateToApply] = useState(null);

  useEffect(() => {
    getDataFromDb();
    if (!intervalIsSet) {
      let interval = setInterval(getDataFromDb, 1000);
      setIntervalIsSet(interval);
    }
    return function unmounting() {
      if (intervalIsSet) {
        clearInterval(intervalIsSet);
        setIntervalIsSet(null);
        console.log("unmonting now");
      }
    };
  }, []);

  const getDataFromDb = () => {
    axios
      .get("http://localhost:3001/api/getData")
      .then(res => {
        setDataState([...res.data.data]);
      })
      .catch(err => console.log("axios error", err));
  };

  const putDataToDB = message => {
    let currentIds = dataState.map(data => data.id);
    let idToBeAdded = 0;
    while (currentIds.includes(idToBeAdded)) {
      ++idToBeAdded;
    }
    const newobj = {
      id: idToBeAdded,
      message: message
    };
    setDataState([...dataState, newobj]);
    axios
      .post("http://localhost:3001/api/putData", newobj)
      .then(res => console.log(res))
      .catch(err => console.log(err));
  };

  const deleteFromDB = message => {
    parseInt(idToDelete);
    let objectToDelete = null;
    dataState.forEach(dat => {
      if (dat.id == idToDelete) {
        objectToDelete = dat._id;
      }
    });

    axios.delete("http://localhost:3001/api/deleteData", {
      data: {
        id: objectToDelete
      }
    });
  };

  const updateDB = (idToUpdate, updateToApply) => {
    let objIdToUpdate = null;
    parseInt(idToUpdate);
    dataState.forEach(dat => {
      if (dat.id == idToUpdate) {
        objIdToUpdate = dat._id;
      }
    });

    axios.post("http://localhost:3001/api/updateData", {
      id: objIdToUpdate,
      update: { message: updateToApply }
    });
  };

  return (
    <div>
      <ul>
        {dataState.length <= 0
          ? "NO DB ENTRIES YET"
          : dataState.map((dat, i) => (
              <li key={i} style={{ padding: "10px" }}>
                <span style={{ color: "gray" }}> id: </span> {dat.id} <br />
                <span style={{ color: "gray" }}> data: </span>
                {dat.message}
              </li>
            ))}
      </ul>
      <div style={{ padding: "10px" }}>
        <input
          type="text"
          onChange={e => setMessage(e.target.value)}
          placeholder="add something in the database"
          style={{ width: "200px" }}
        />
        <button onClick={() => putDataToDB(message)}>ADD</button>
      </div>
      <div style={{ padding: "10px" }}>
        <input
          type="text"
          style={{ width: "200px" }}
          onChange={e => setIdToDelete(e.target.value)}
          placeholder="put id of item to delete here"
        />
        <button onClick={() => deleteFromDB(idToDelete)}>DELETE</button>
      </div>
      <div style={{ padding: "10px" }}>
        <input
          type="text"
          style={{ width: "200px" }}
          onChange={e => setIdToUpdate(e.target.value)}
          placeholder="id of item to update here"
        />
        <input
          type="text"
          style={{ width: "200px" }}
          onChange={e => setUpdateToApply(e.target.value)}
          placeholder="put new value of the item here"
        />
        <button
          onClick={() =>
            updateDB(idToUpdate, updateToApply)
          }
        >
          UPDATE
        </button>
      </div>
    </div>
  );
};

export default App;


```
