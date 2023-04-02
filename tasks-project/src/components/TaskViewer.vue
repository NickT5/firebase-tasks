<template>
    <div id="taskViewer">
        <h1>🚀 Task Viewer</h1>
        <input type="text" placeholder="Add a task" ref="inputNewTask"><button @click="addTask">Add new task</button>
        <ul>
            <div class="flexy" v-for="(task, index) in tasks" :key="index">
                <li :class="{'taskDone': task.done}">{{ index }}. {{ task.description }}. {{ task.id }} {{ task.done }}</li>
                <button @click="toggleDone(task.id, task.done)">&check;</button>
                <button @click="removeTask(task.id)">&cross;</button>
            </div>
        </ul>
    </div>
</template>

<script>
      // Import the functions you need from the SDKs you need
      import { initializeApp } from "firebase/app";
      // import { getAnalytics } from "firebase/analytics";
      import { getDatabase, ref, push, update, remove, onValue} from "firebase/database";
      
      // TODO: Add SDKs for Firebase products that you want to use
      // https://firebase.google.com/docs/web/setup#available-libraries
      
      
      // Your web app's Firebase configuration
      // For Firebase JS SDK v7.20.0 and later, measurementId is optional
      const firebaseConfig = {
        apiKey: "AIzaSyBgBy8pvFJjPDlk5x1IcdFbSYUWiW0tCIU",
        authDomain: "my-firebase-d131d.firebaseapp.com",
        databaseURL: "https://my-firebase-d131d-default-rtdb.europe-west1.firebasedatabase.app",
        projectId: "my-firebase-d131d",
        storageBucket: "my-firebase-d131d.appspot.com",
        messagingSenderId: "5753391844",
        appId: "1:5753391844:web:e4a7466bbd2acbb0827af1",
        measurementId: "G-4YJVRBFZCL"
      };
      
      
      // Initialize Firebase
      const app = initializeApp(firebaseConfig);
      // const analytics = getAnalytics(app);
      const db = getDatabase(app);
      ///////////////////////////////////////////////////////////////////////////////////////////

    export default {
        name: "TaskViewer",
        data(){
            return{
                tasks: []
            }
        },
        mounted() {
            let localTasks = [];
            const refTasks = ref(db, '/tasks');

            onValue(refTasks, (snapshot) => {
                console.log("Detected value change!")
                snapshot.forEach((childSnapshot) => {
                    localTasks.push({
                        'id': childSnapshot.key,
                        'description': childSnapshot.val().description,
                        'done': childSnapshot.val().done,
                        'createdAt': childSnapshot.val().createdAt
                    });
                });
                this.tasks = [...localTasks];  // spread operator needed for Vue object to re-render.
                localTasks = [];               // reset, because the array init happens only at mounted moment.
            })
        },
        
        methods: {            
            addTask() {
                const input = this.$refs.inputNewTask;
                const newTask = {"description": input.value, "done": false};

                // // Add to vue data
                // this.tasks.push(newTask);

                // Add a new task under /tasks/{unique id}
                push(ref(db, '/tasks'), newTask)
                .then(console.log("Inserted a new task in the database."))
                .catch((error) => {alert(error)});

            },
            toggleDone(id, done) {
                // this.tasks[index].done = !done;

                update(ref(db, "/tasks/" + id), {
                    done: !done
                })
                .then(()=>{console.log("Data updated successfully!");})
                .catch((error)=>{
                    alert(error)
                });

            },
            removeTask(id) {
                // Remove one task.
                // this.tasks.splice(index, 1);

                remove(ref(db, "/tasks/" + id))
                .then(()=>{
                    console.log("Removed data with id " + id);
                })
                .catch((error)=>{alert(error);});
            }
        }
    }
</script>


<style scoped>
#taskViewer{
    box-sizing: border-box;
}

.flexy{
    display: flex;
    margin-top: 10px;
}

ul {
    list-style-type: none;
    padding: 0;
    margin: 0;
}

li {
    padding: 20px;
    background-color: aquamarine;
    border-radius: 5px;
}

input {
    width: 50%;
    padding: 10px;
}

button {
    background-color: slateblue;
    color: seashell;
    padding: 10px;
}

button:hover{
    background-color: darkorange;
}

.taskDone {
    text-decoration: line-through;
}
</style>