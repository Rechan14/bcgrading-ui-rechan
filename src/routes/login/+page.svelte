<script>
    import { onMount } from 'svelte';
    import { goto } from '$app/navigation';
    import 'bootstrap/dist/css/bootstrap.min.css';
    import backgroundImage from '$lib/images/background.png';
    import logo from '$lib/images/bc-logo.png';

    let username = '';
    let password = '';
    let error = '';
    

  
    onMount(async () => {
 
  if (!localStorage.getItem('x')) {
    localStorage.setItem('x', 'true');
    // Reload the page
    window.location.reload();
  } else {
    localStorage.removeItem('x');
  }
});




    async function handleLogin() {
        try {
            const response = await fetch('http://localhost:4000/accounts/authenticate', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({ username, password })
            });

            if (!response.ok) {
                throw new Error('incorrect username and/or passowrd :(');
            }

            const data = await response.json();
            const role = data.role;

            // Save JWT token to local storage or cookies as needed
            localStorage.setItem('jwtToken', data.jwtToken);

            // Redirect based on the role
            if (role === 'Admin') {
                goto('/Admin');
            } else if (role === 'Registrar') {
                goto('/Registrar');
            } else if (role === 'Teacher') {
                goto('/Teacher');
            } else if (role === 'Student') {
                goto('/Student');
            } else {
                throw new Error('Unknown role');
            }
        } catch (err) {
            error = err.message;
        }
    }
</script>




<!-- 
<h3 class="card-header">Login</h3>


<div class="card-body">
<form on:submit|preventDefault={handleLogin}>

    
    <label for="username">Username:</label>
    <input id="username" bind:value={username} required />

    <label for="password">Password:</label>
    <input id="password" type="password" bind:value={password} required />

    <button type="submit">Login</button>
</form>
</div> -->


<!-- <div class="parent-container">
<div class="form__container">
    <form on:submit|preventDefault={handleLogin}>
      <div class="header__form">
       
        <img src={logo} alt="Logo" />
      
      </div>

      <div class="main__input__container">
      
        <div class="input-container">
          <input type="text" id="username" bind:value={username} required />
          <label for="username" class="label">Username</label>
          <div class="underline"></div>
        </div>
        <div class="input-container">
          <input type="password" id="password" bind:value={password} required/>
          <label for="password" class="label">Password</label>
          <div class="underline"></div>
   
        </div>
      
        <div class="input-container">
            {#if error}
          <p style="color: red">{error}</p>
            {/if}
        </div>
        <div class="button">
          <button type="submit" class="sign__in__button">Sign In</button>
        </div>

        
      </div>
    </form>
  </div>

</div> -->

<section class="vh-100">
  <div class="container-fluid p-0">
    <div class="row">
    
      <div class="col-sm-5 text-black">

        <div class="px-5 ms-xl-4 mt-5">
          <img src="/src/lib/images/bc-logo.png" alt="" class="logo">
        </div>

        <div class="d-flex align-items-center h-custom-2 px-5 ms-xl-4 mt-5 pt-5 pt-xl-0 mt-xl-n5">

          <form style="width: 23rem;" on:submit|preventDefault={handleLogin}>

        
              <!-- <input type="text" id="username" bind:value={username} required />
              <label for="username" class="label">Username</label>
              <div class="underline"></div> -->

              <div class="form-floating mb-3">
                <input type="text" id="username" bind:value={username} required class="form-control" placeholder="Username" />
                <label class="form-label" for="username">Username</label>
              </div>

              <div class="form-floating mb-3">
                <input type="password" id="password" bind:value={password} required class="form-control" placeholder="password" />
                <label class="form-label" for="password">Password</label>
              </div>
       
        
          
            <div class="input-container">
                {#if error}
              <p style="color: red">{error}</p>
                {/if}
            </div>
        
              
            <div class="pt-1 mb-4 d-flex justify-content-between align-items-center">
              <button class="btn btn-primary" type="submit">Login</button>
              <!-- <a href="/">Forgot password?</a> -->
            </div>
    
          </form>

        </div>

      </div>
      <div class="col-sm-7 px-0 d-none d-sm-block ">
        <!-- svelte-ignore a11y-img-redundant-alt -->
        <!-- <img src="/src/lib/images/male-team-illustration.png"
          alt="Login image" class="w-100 vh-100" style="object-fit: cover; object-position: center;"> -->

          <img src="https://mis.benedictocollege.edu.ph/assets/bene-school-815be6299a10bcc76c09c819b5a1f49beb216565f77ba611270de3b63f1b33f6.jpg"
          alt="Login image" class="w-100 vh-100" style="object-fit: cover; object-position: center">
      </div>
    </div>
  </div>
</section>



<style>

*,
*::before,
*::after {
  box-sizing: border-box;
}
* {
  margin: 0;
}


.logo{
  width: 120px;
  height: 120px;
}

section{
  background-color: rgba(255, 255, 255, 0.35);
}

.form-control{
  outline: none !important;
}

label{
  color: gray !important;
}

</style>