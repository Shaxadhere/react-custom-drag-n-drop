```
import { useEffect, useState } from "react";
import "./App.css"
import logos from "./logos";
import { folders } from "./logos";


function App() {

  const [list, setList] = useState([])
  const [dragInfo, setDragInfo] = useState({
    oldFolder: "",
    newFolder: "",
    item: {}
  })

  const allowDrop = (ev) => {
    ev.preventDefault();
  }

  const drag = (ev, folderName, item) => {
    setDragInfo({ ...dragInfo, oldFolder: folderName, item: item })
  }

  const drop = (ev, folderName) => {
    ev.preventDefault();
    setDragInfo({ ...dragInfo, newFolder: folderName })

    const _list = list?.map((item) => {
      if (dragInfo.oldFolder === folderName) {
        console.log("Dropped inside its old folder")
        return item
      }
      else if (item.folderName === dragInfo.oldFolder) {
        console.log("removing from old folder")
        const index = item.logos.findIndex(i => i == dragInfo.item)
        item.logos.splice(index, 1)
        return item
      }
      else if (item.folderName === folderName) {
        console.log("adding inside new folder")
        item.logos.push(dragInfo.item)
        return item
      }
      else {
        console.log("no action happenned")
        return item
      }
    })
    setList([..._list])
  }

  useEffect(() => {
    const _folders = folders.map((folder) => {
      const _logos = logos.filter((logo, index) => {
        if (logo.folderName === folder) {
          return logo
        }
      })
      return { folderName: folder, logos: _logos }
    })
    setList([..._folders])
  }, [])

  return (
    <div className="App">
      <div style={{ display: "flex" }}>
        {list?.map((item, index) =>
          <div key={index} style={{ margin: 20 }}>
            <h1>{item.folderName}</h1>
            <div className="container" onDrop={(e) => drop(e, item.folderName)} onDragOver={(e) => allowDrop(e)}>
              {item.logos.map((logo, index) =>
                <div
                  key={index}
                  className="item"
                  id={logo.title}
                  data-folder={item.folderName}
                  data-item={logo.companyName}
                  draggable="true"
                  onDragStart={(e) => drag(e, item.folderName, logo)}
                >
                  <img src={logo.image} width="336" height="69" />
                </div>
              )}
            </div>
          </div>
        )}
      </div>
    </div>
  );
}

export default App;
```