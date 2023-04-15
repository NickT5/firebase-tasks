<template>

    <div class="mt-10 flex flex-wrap justify-evenly debug">

        <div id="taskViewer" class="">
            <!-- Title -->
            <h1 class="text-4xl font-bold mb-8 debug">🚀 Task Viewer</h1>

            <!-- Insert a new task -->
            <div class="mt-15 mb-10">
                <div class="mb-3">
                    <label class="font-bold mb-20">Description</label>
                    <input type="text" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" placeholder="Description" ref="inputNewTask">
                </div>
                <div class="mb-3">
                    <label class="font-bold mt-6 !important">Priority</label>
                    <select class="w-full bg-gray-200 border border-gray-200 text-gray-700 py-3 px-4 pr-8 rounded focus:bg-white focus:border-gray-500">
                        <option value="high">High</option>
                        <option value="medium">Medium</option>
                        <option value="low">Low</option>
                    </select>
                </div>
                <button class="inline-flex items-center btn btn-blue" @click="addTask">
                    <svg class="fill-current w-4 h-4 mr-2" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"><rect x="8" width="4" height="20"/><rect y="8" width="20" height="4"/></svg>
                    <span>Add</span> 
                </button>

            </div>

            <!-- List of tasks -->
            <div class="debug">
                <ul>
                    <div class="mb-2 debug w-96" v-for="(task, index) in tasks" :key="index">
                        <div class="flex flex-row">
                            <!-- prio -->
                            <div id="prio" class="prio prio-high"></div>
                            <div class="bg-gray-200 ps-2 py-2 h-36 min-h-full w-full">
                                <!-- tags -->
                                <div class="tags mb-4">
                                    <div :class="{'tagDone': task.done}" class="outline outline-gray-300 inline-block rounded-full px-3 py-1 text-xs font-semibold text-gray-700 mr-2">DONE</div>

                                </div>
                                <!-- task content -->
                                <li class="debug">{{ task.description }}. {{ task.id }} {{ task.done }}</li>
                            </div>
                        </div>
                        <!-- buttons -->
                        <div class="flex justify-end">
                            <button class="btn btn-blue" @click="toggleDone(task.id, task.done)">&check;</button>
                            <button class="btn btn-red ml-2" @click="removeTask(task.id)">&cross;</button>
                        </div>
                    </div>
                </ul>
            </div>

        </div>

        <div id="stats" class="debug">
            <!-- Title -->
            <h1 class="text-4xl font-bold mb-8">Stats</h1>
            
            <!-- Content -->
            <div class="grid grid-cols-2 gap-4 text-2xl">
                <div>
                    <h3 class="mb-4">Total</h3>                    
                    <h3 class="mb-4"># completed</h3>                    
                    <h3 class="mb-4"># uncompleted</h3>                    
                </div>
                <div>
                    <h3 class="mb-4">3</h3>                    
                    <h3 class="mb-4">3</h3>                    
                    <h3 class="mb-4">3</h3>                    
                </div>
            </div>
        </div>

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
                tasks: [
                {"description": 'Getting started with Firebase', "done": true},
                {"description": 'Getting started with Vue', "done": true},
                {"description": 'Getting started with Figma', "done": false},
                {"description": 'Getting started with Tailwind', "done": false}
            ]
            }
        },
        mounted() {
            return /* eslint-disable */
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
    /* box-sizing: border-box; */
}
/* 
ul {
    list-style-type: none;
    padding: 0;
    margin: 0;
} */

/* li {
    padding: 20px;
    background-color: aquamarine;
    border-radius: 5px;
} */

input {
    /* width: 50%; */
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

.tagDone{
    @apply bg-green-500 outline outline-green-300 text-white;
}

.debug{
    /* @apply outline outline-lime-300; */
}

.btn {
    @apply font-bold py-2 px-4 rounded;
  }
  .btn-blue {
    @apply bg-blue-500 text-white;
  }
  .btn-blue:hover {
    @apply bg-blue-700;
  }

  .btn-red{
    @apply bg-red-500 text-white;
  }

  .btn-red:hover{
    @apply bg-red-700;
  }


  .prio{
    @apply w-6;
  }
  .prio-high{
    @apply bg-red-600;
  }

  .prio-low{
    @apply bg-blue-400;
  }

</style>