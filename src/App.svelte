<script>
  import { FirebaseApp, User, Doc, Collection } from "sveltefire";
  import firebase from "firebase/app";
  import "firebase/firestore";
  import "firebase/auth";
  import "firebase/performance";
  import "firebase/analytics";
  import AddMember from "./AddMember.svelte";

  const firebaseConfig = {
    apiKey: "AIzaSyDYiYRMlAZpEXya9aVUhv9cpJwJL4Oz7gM",
    authDomain: "dazzling-torch-8270.firebaseapp.com",
    databaseURL: "https://dazzling-torch-8270.firebaseio.com",
    projectId: "dazzling-torch-8270",
    storageBucket: "dazzling-torch-8270.appspot.com",
    messagingSenderId: "935228019520",
    appId: "1:935228019520:web:0d827d87281eb216e920a5",
  };

  firebase.initializeApp(firebaseConfig);

  let email = "";
  let password = "";
  let repassword = "";
  let isSignup = false;
  let firstName = "";
  let lastName = "";
  let ss = false;
  let signUpHasError = false;
  let errorMessage = "";

  function signUp() {
    firebase
      .auth()
      .createUserWithEmailAndPassword(email, password)
      .catch(function (error) {
        signUpHasError = true;
        errorMessage = error.message;
      });
  }

  function login() {
    firebase
      .auth()
      .signInWithEmailAndPassword(email, password)
      .catch(function (error) {
        // Handle Errors here.

        var errorCode = error.code;

        var errorMessage = error.message;
        // ...
      });
  }

  function createAccount() {
    if (validate()) {
      signUp();
    }
  }

  function validate() {
    let result = true;
    if (password != repassword) {
      result = false;
      signUpHasError = true;
      errorMessage = "Password did not match!";
    }

    return result;
  }
</script>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  h1,
  em {
    color: #0d8d23;
  }

  hr {
    height: 1px;
    border: none;
    background: rgb(195, 195, 195);
  }

  .s1 {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    align-content: center;
    flex-wrap: wrap;
    margin-top: 4rem;
  }

  .s2 {
    margin: 1rem;
    width: 180px;
  }

  .s4 {
    color: red;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>

<!-- STYLES -->
<!-- STYLES ENDS -->

<!-- HTML -->

<main>

  <!-- {#if !firebaseConfig.projectId}
    <strong>Step 0</strong>
    Create a
    <a href="https://firebase.google.com/">Firebase Project</a>
    and paste your web config into
    <code>App.svelte</code>
    .
  {/if} -->

  <FirebaseApp {firebase}>

    <h1>CARE TO CA$H</h1>

    <User let:user let:auth>
      Hi 😀!
      <em>{user.email}</em>
      <button on:click={() => auth.signOut()}>Sign Out</button>

      <div class="s1" slot="signed-out">
        {#if isSignup === false}
          <input
            class="s2"
            type="email"
            placeholder="Email"
            bind:value={email} />
          <input
            class="s2"
            type="password"
            placeholder="Password"
            bind:value={password} />

          <button class="s2 btn btn-danger" on:click={login}>Login</button>

          <p>
            Do not have an Account ?
            <a
              href="javascript:;"
              on:click={() => {
                isSignup = true;
              }}>
              create one
            </a>
          </p>
        {:else if isSignup === true}
          <input
            class="s2"
            type="email"
            placeholder="Email"
            bind:value={email} />

          <input
            class="s2"
            type="password"
            placeholder="Password"
            bind:value={password} />

          <input
            class="s2"
            type="password"
            placeholder="Re-Enter Password"
            bind:value={repassword} />

          <button on:click={createAccount} class="s2 btn btn-danger">
            Create My Account
          </button>
          {#if signUpHasError}
            <em class="s4">{errorMessage}</em>
          {/if}
        {/if}

      </div>

      <!-- <button on:click={signUp}>Sign Up</button> -->

      <hr />

      <AddMember />

      <Doc path={`posts/${user.uid}`} let:data={post} let:ref={postRef} log>

        <h2>{post.title}</h2>

        <p>
          Document created at
          <em>{new Date(post.createdAt).toLocaleString()}</em>
        </p>

        <span slot="loading">Loading post...</span>
        <span slot="fallback">
          <button
            on:click={() => postRef.set({
                title: '📜 I like Svelte',
                createdAt: Date.now(),
              })}>
            Create Documentd
          </button>
        </span>
        4. 💬 Get all the comments in its subcollection
        <h3>Comments</h3>
        <Collection
          path={postRef.collection('comments')}
          query={(ref) => ref.orderBy('createdAt')}
          let:data={comments}
          let:ref={commentsRef}
          log>

          {#if !comments.length}No comments yet...{/if}

          {#each comments as comment}
            <p>
              ID:
              <em>{comment.ref.id}</em>
            </p>
            <p>
              {comment.text}
              <button on:click={() => comment.ref.delete()}>Delete</button>
            </p>
          {/each}

          <button
            on:click={() => commentsRef.add({
                text: 'dasfasdf',
                createdAt: Date.now(),
              })}>
            Add Comment
          </button>

          <span slot="loading">Loading comments...</span>

        </Collection>
      </Doc>

    </User>

  </FirebaseApp>

</main>

<!-- HTML ENDS -->
