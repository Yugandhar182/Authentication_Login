
<script>
    import { onMount } from "svelte";
    import { auth } from '$lib/firebase/firebase.js';
   import { goto } from '$app/navigation';
  
   import Button from "$lib/buttons/button.svelte";
  
  
   
   let jsonData = [];
   let gridData = [];
   
   onMount(async () => {
     const response = await fetch(
       "https://api.recruitly.io/api/job?apiKey=TEST64518616D4CF145D4E20BD47169EA7229BA3"
     );
     const responseData = await response.json();
     jsonData = responseData.data;
     console.log(jsonData);
   
     gridData = jsonData.map((item) => ({
       id: item.id,
       reference: item.reference,
       title: item.title,
     status: item.status,
       jobType: item.jobType,
     }));
   
     const dataGrid = new DevExpress.ui.dxDataGrid(document.getElementById("dataGrid"), {
       dataSource: gridData,
       columns: [
         { dataField: "id", caption: "ID", width: 250 },
         { dataField: "reference", caption: "Reference", width: 200 },
         { dataField: "title", caption: "Title", width: 200 },
         { dataField: "status", caption: "Status", width: 200 },
         { dataField: "jobType", caption: "JobType", width: 150 },
         // Define other columns as needed
       ],
      
       showBorders: true,
       filterRow: {
         visible: true,
       },
       editing: {
         allowDeleting: true,
         allowAdding: true,
         allowUpdating: true,
         mode: "popup",
         form: {
           labelLocation: "top",
         },
         popup: {
           showTitle: true,
           
         },
         texts: {
           saveRowChanges: "Save",
           cancelRowChanges: "Cancel",
           deleteRow: "Delete",
           confirmDeleteMessage: "Are you sure you want to delete this record?",
         },
       },
       paging: {
         pageSize: 10,
       },
       onRowInserting: async (e) => {
         console.log("Data being sent to API:", e.data);
         try {
           const response = await fetch(
             "https://api.recruitly.io/api/job?apiKey=TEST64518616D4CF145D4E20BD47169EA7229BA3",
             {
               method: "POST",
               headers: {
                 "Content-Type": "application/json",
               },
               body: JSON.stringify(e.data),
             }
           );
   
           const responseData = await response.json();
           if (response.ok) {
             e.data.reference = responseData.reference;
             gridData.push(e.data);
             dataGrid.refresh();
           } else {
             console.error("Failed to add record:", responseData.error);
           }
         } catch (error) {
           console.error("Failed to add record:", error);
         }
       },
       onRowUpdating: async (e) => {
         try {
           console.log(e);
           var newData = {
             id: e.key.id,
             reference: e.newData.reference === undefined ? e.oldData.reference : e.newData.reference,
             title: e.newData.title === undefined ? e.oldData.title : e.newData.title,
           status: e.newData.status === undefined ? e.oldData.status : e.newData.status,
             jobType: e.newData.jobType === undefined ? e.oldData.jobType : e.newData.jobType,
           }
   
           console.log(newData)
           const response = await fetch(
             `https://api.recruitly.io/api/job?apiKey=TEST64518616D4CF145D4E20BD47169EA7229BA3`,
             {
               method: "POST",
               headers: {
                 "Content-Type": "application/json",
               },
               body: JSON.stringify(newData),
             }
           );
           const responseData = await response.json();
           if (response.ok) {
             const updatedItemIndex = gridData.findIndex((item) => item.id === e.key);
             gridData.push(e.newData);
             gridData[updatedItemIndex] = e.newData;
             dataGrid.refresh();
           } else {
             console.error("Failed to update record:", responseData.error);
           }
         } catch (error) {
           console.error("Failed to update record:", error);
         }
       },
       onRowRemoving: async (e) => {
         console.log("Data being sent to API:", e.data);
         try {
           const response = await fetch(
             `https://api.recruitly.io/api/job/${e.data.id}?apiKey=TEST64518616D4CF145D4E20BD47169EA7229BA3`,
             {
               method: "DELETE",
             }
           );
           if (response.ok) {
             const removedItemIndex = gridData.findIndex((item) => item.id === e.key);
             if (removedItemIndex > -1) {
               gridData.splice(removedItemIndex, 1);
               dataGrid.refresh();
             }
           } else {
             console.error("Failed to delete record.");
           }
         } catch (error) {
           console.error("Failed to delete record:", error);
         }
       },
       onInitialized: () => {
   
       },
     });
   });
  
   
  
 
   let isBlue = true; 
 
   onMount(() => {
     // Create an interval to toggle the color every 2 seconds
     const interval = setInterval(() => {
       isBlue = !isBlue;
     }, 1000);
 
     // Clean up the interval when the component is unmounted
     return () => clearInterval(interval);
   });
 
 
 
 
  function Homepage() {
    goto('/Homepage');
  }
 
 
 
 </script>
 
 <main>
  <Button/>
    <h2 class="h2" style="color: blue;">Job list</h2>
  <div id="dataGrid" class="datagrid-container"></div>

 
 </main>

 
 <img class="imag" src="https://img.icons8.com/?size=1x&id=2797&format=png" style="color:white;" alt="Click me" on:click={Homepage} />

 
 

 
 <style>
 

 .datagrid-container{
    margin-top:10px ;
    background-color: rgb(185, 127, 255);
    margin-right: 10px;
    margin-left: 400px;
  
  }
  .imag {
    position: absolute;
    top: 10px;
    left: 10px;
    height: 30px;
  
  }
 
 
   .h2 {
    margin-left: 700px;
    margin-top: 90px;
    font-weight: bold;
    font-size: 20px;
  
  }




 </style>
