<script>
    import { getAuth, onAuthStateChanged } from "firebase/auth";
    import {onSnapshot, doc, getDoc, collection, query, where,} from "firebase/firestore";
    import {_firestore_} from "../../routes/firebase/firebase.js"; 
    import { onDestroy } from "svelte";
    import "./cardsglobal.css";
    import SkillBubbles from "../elements/skillbubbles.svelte";

    // Grabbing auth from firebase
    const auth = getAuth();
    let _user;
    let _uid;
    let _email;
    onAuthStateChanged(auth, (user) => {
        _user = user;
    if (user) {
        _uid = user.uid;
        _email = user.email;
        // If user is authenticated, assign their data into global variables.
    } else {
        // User is signed out (logic to be included here)
    }
    });

    // Creating array 
    let _userid;
    let _userdetails = [];

    //Reading Collection 'AllUsers' from Firestore
    const _collection = collection(_firestore_, "AllUsers");
    // Creating snapshot from firestore 
    const _snapshot = onSnapshot(_collection, (querySnapshot) => {
      let _fireuser = []; // Local array[]
      querySnapshot.forEach((doc) => {
          let details = {...doc.data(), userid: doc.id} // Details = data from firestore
          _fireuser = [details, ..._fireuser]; // Assigning details to local []
          _userdetails.push(doc.data()); // Pushing data to global []
          _userdetails = _fireuser; // Assinging local [] details to global [] details
          _userid = doc.data().userid; // Grabbing userid from firestore and assinging it to global var
      });
        console.log(_fireuser)
    });

    onDestroy(_snapshot); // Destroying snapshot
</script>

<!-- HTML BEGINS -->
{#each _userdetails as _info}
<!-- If block, finding the relevant userdata based on document auth -->
{#if _info.userid == _uid && _info.email == _email} 


<div class="skill-body-container">
    <h2 class="card-h2">Skills</h2>
    <div class="card-container">
        <div class="vertical-flex-control-box">
            <div class="title-display-flex-top">
                <h4 class="card-h5">Top Trait</h4>
                <h3 class="card-display-text">{_info._traits.trait1}</h3>
            </div>
           </div>
           <div class="margin-break">
            <h4 class="card-h5-skills">Skills</h4>
           <div class="horizontal-flex">
            <div class="left-skills">
                <SkillBubbles _skill={_info._skills.skill1}/>
                <SkillBubbles _skill={_info._skills.skill2}/>
                <SkillBubbles _skill={_info._skills.skill3}/>
                <SkillBubbles _skill={_info._skills.skill4}/>
               
            </div>
            <div class="right-skills">
              
                <SkillBubbles _skill={_info._skills.skill5}/>
                <SkillBubbles _skill={_info._skills.skill6}/>
                <SkillBubbles _skill={_info._skills.skill7}/>
                <SkillBubbles _skill={_info._skills.skill8}/>

            </div>
    </div>
</div>
    </div>
</div>

<!-- Each loop, executing the _userdetails[] array -->
<!-- Correct data displaying -->
{/if}
{/each}
