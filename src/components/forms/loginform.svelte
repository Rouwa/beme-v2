<script>
  import {goto} from '$app/navigation';
  import {getAuth, signInWithEmailAndPassword, createUserWithEmailAndPassword } from 'firebase/auth';
  import '../../../src/global.css';
  import {_firestore_} from "../../routes/firebase/firebase.js"; //Importing Firestore component that was initialised in firebase.js
  import {collection, addDoc, doc, setDoc,getDoc,onSnapshot} from "firebase/firestore"; // Importing functions from Firestore
  // Declaring global varibales
  export let title;
  const auth = getAuth();
  
  let _info = {
      email: "",
        _userabout:{
        currentrole: "",
        interest: "",
        level_experience: "",
        goals: "" },

      _myprofile:{
        firstname: "",
        lastname: "",
        workemail: "",
        phone: "",
        bio:""
      }, 
      _education:{
        qualification: "",
        study_level: "",
        study_place: "",
        fav_subject: "",
        year: ""},
      
      _traits:{trait1:""},
      _skills:{skill1:"",skill2:"",skill3:"",skill4:"", skill5:"",skill6:"",skill7:"",skill8:""},

      _favexperience:{
        worktitle: "",
        time: "",
        newskill: "",
        favskillsapplied:{favskill1:"",favskill2:"",favskill3:"",favskill4:""}
      },
      _experience1:{role: "",
      company: "",
      date: "",
      skill_applied:""},

    };

  const addUserToFirestore = async () => {
      try {
        await setDoc(doc(_firestore_, "AllUsers",localStorage.getItem('uid')), {
          ..._info,
          createdAt: Date.now(),
          userid: localStorage.getItem('uid'),
        }).then(() =>
        {                 
          onSnapshot(doc(_firestore_, "AllUsers", localStorage.getItem('uid')), (doc) => {
          console.log("Current data from LOGIN: ", doc.data());
        });
        
        })
        // Waiting to catch errors
      } catch (error) {
        console.error(error);
      }
  
    };

    const handleSubmit = () => {
      if (title == "Enter your details to login."){
        ; // Do nothing
      }
      else{ // Add user to Firestore
        addUserToFirestore();
      }
};

 
  function Login()
  {
      // Declaring local variables to grab info from UI &authenticate user
      let email = document.getElementById('login-email-input').value;
      let password = document.getElementById('login-password-input').value;
      // If title & login match, authenticate the user and sign them in using Firebase Auth SDKs & Cloud Functions
      if (title == "Enter your details to login."){
          signInWithEmailAndPassword(auth, email, password) // Signing user in
          // Promise (.then) is resolved
          .then((userCredential)=>{
              const user = userCredential.user
              // Storing user ID locally (allowing for access of user's files locally)
              // Tokens & user's private data will be stored in Firebase
              localStorage.setItem('uid', user.uid);
              localStorage.setItem('email', user.email)
              // Directing to "home page" aka project route 
              goto("/")
              // Catching errors
          }).catch((error) => {
              console.error(error)
                alert("The system doesn't recognise the username password combination, please try again."); 
                      
          })
      }
      else { // If user does not exist: create new user
          createUserWithEmailAndPassword(auth, email, password) 
          .then((userCredential)=>{
              const user = userCredential.user
              localStorage.setItem('uid', user.uid);
              localStorage.setItem('email', user.email)
              goto("/")
              handleSubmit();
              // Catching errors
          }).catch((error) => {
              console.error(error)
              alert("We could not sign you up! You might already be a member :)");
          })
      }
  }
</script>

<!--HTML TEMPLATE BEGINS-->
<body>
<div class="page-template">
<div class="login">
  <h1 id='local-h1'> Everything you need in one place. </h1>
  <h3 id='local-h3'> Build and share your portfolio with ease. </h3>
  <div class="card">
    <div class="card-body login-form">
      <form on:submit|preventDefault={Login}>
        <h5 class="card-title">{title}</h5>
        <div class="mb-3">
          <label for="login-email-input" class="form-label">Email address</label>
          <input
            bind:value={_info.email}
            type="email"
            class="form-control"
            id="login-email-input"
            aria-describedby="emailHelp"
            placeholder="Email Address"
          />
          {#if title != "Enter your details to login."}
            <div id="emailHelp" class="form-text">
              We'll never share your email with anyone else.
            </div>

          {/if}
        </div>
        <div class="mb-3">
          <label for="login-password-input" class="form-label">Password</label>
          <input
            type="password"
            class="form-control"
            id="login-password-input"
            placeholder="Password"
          />
        </div>
        <div class="horizontal-flex">
            {#if title == "Enter your details to login."}
          <div class="button-signup">
            <a href="/authenticate/signup"  class="signup-text">Sign Up</a>
            </div>
            {/if}
        <button type="submit" class="button">Let's Go!</button>
      </div>
      </form>
    </div>
  </div>
</div>
</div>
</body>


<!-- CSS BEGINS -->
<style>
  .page-template{
    display: flex;
    flex-direction: column;
    width: 100%;   
  }
  .login{
    color: var(--dark);
    font-weight: 500;
    width: 100%;
  }
  .card {
    width: 100%;
    margin: 0 auto;
  }
  #local-h1{
    margin-top: 1.23rem;
    background: -webkit-linear-gradient(45deg, #5271FF, #38B6FF);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    font-family: 'Montserrat';
    font-style: normal;
    font-weight: 700;
    font-size: 2.3rem;
    line-height: 44px;
  }
  #local-h3{
    margin-top: 1.23rem;
    font-family: 'Montserrat';
    font-style: normal;
    font-weight: 400;
    font-size: 1rem;
  }
  .card-title, #emailHelp{
    font-family: 'Montserrat';
    font-style: normal;
    font-weight: 500;
    font-size: 0.77rem;
    margin-bottom: 1.23rem;
    margin-top: 0.23rem;
  }
  form{
    margin-top: 0.88rem;
    width: 100%;
  }
  .form-label{
    margin-top: 0.23rem;
    margin-bottom: 0.23rem;
    font-family: 'Montserrat';
    font-style: normal;
    font-weight: 700;
    font-size: 0.77rem;
    letter-spacing: 0.23px;
    background: -webkit-linear-gradient(45deg, #5271FF, #38B6FF);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
  }
  .button{
    font-family: 'Montserrat';
    font-style: normal;
    font-weight: 700;
    letter-spacing: 0.23px;
    width: 100%;
    font-size: large;
    margin-top: 2.23rem;
    color: white;
    background-image: linear-gradient(
    45deg,
    hsl(237deg 81% 60%) 0%,
    hsl(218deg 94% 54%) 20%,
    hsl(210deg 100% 50%) 40%,
    hsl(205deg 100% 50%) 60%,
    hsl(201deg 100% 50%) 80%,
    hsl(202deg 100% 61%) 100% );
    border-radius: 23rem;
    text-align: right;
    padding-right: 2.23rem;
  }
  .button:hover{
    opacity: 88%;
  }
  .button-signup{
    padding-top: 0.7rem;
    background: -webkit-linear-gradient(45deg, #5271FF, #38B6FF);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    border: 1.88px solid var(--lighttext);
    width: 10.23rem;
    margin-top: 2.23rem;
    border-radius: 0.55rem;
    text-align: left;
    padding-left:1rem;
    margin-right: 1.23rem;
  }
  .button-signup:hover{
    border-radius: 23rem;
  }
  .signup-text{
    font-family: 'Montserrat';
    font-style: normal;
    font-weight: 700;
    letter-spacing: 0.23px;
    font-size: large;
  }
  .login-form {
    width: 100%;
    margin: 0 auto;
    padding-top: 0.55rem;
  }
  input{
    font-family: 'Montserrat';
    font-style: normal;
    font-weight: 700;
    height: 3.23rem;
    background: 
      linear-gradient(var(--light), var(--light)) padding-box,
      linear-gradient(90deg, #5271FF 0%, #38B6FF 100%) border-box;
    border-radius: 23rem;
    border: 1.88px solid transparent;
    width: 100%;
    color: var(--purple);
    font-size: large;
  }
  input::placeholder{
    color: var(--lighttext);
    font-size: large;
  }
  input:focus{
    background: 
      linear-gradient(var(--light), var(--light)) padding-box,
      linear-gradient(90deg, #5271FF 0%, #38B6FF 100%) border-box;
    border-radius: 23rem;
    border: 2.3px solid transparent;
}
  .horizontal-flex{
    display: flex;
    justify-content: flex-end;
    justify-content: space-between;
    
  }
  @media only screen and (min-device-width: 320px) and (max-device-width: 480px) {
    .login-form {
      width: 90%;
    }
    .card {
      width: 90%;
    }
  
  }
</style>
