<script>
    import { onMount } from 'svelte';
    import { goto } from '$app/navigation';

    let subjectcode = '';
    let title = '';
    // let username = '';
    // let password = '';
    // let confirmPassword = '';

    let message = '';

  

    async function handleSubmit() {
        // if (password !== confirmPassword) {
        //     message = "Passwords do not match.";
        //     return;
        // }

        // Retrieve JWT token from localStorage
        const token = localStorage.getItem('jwtToken');

        try {
            const response = await fetch('http://localhost:4000/registrar/addsubject', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                    'Authorization': `Bearer ${token}` // Include JWT token
                },
                body: JSON.stringify({
                    subjectcode,
                    title
                })
            });

            if (!response.ok) {
                const errorData = await response.json();
                throw new Error(errorData.message || 'Error creating account');
            }

            const result = await response.json();
            message = 'Subject created/added successfully!';
            goto('/Registrar/AddSubject'); // Redirect to a success page or dashboard
        } catch (error) {
            message = error.message || 'Error creating account';
        }
    }

    function filterInput(event) {
    const input = event.target;
    // Convert input value to uppercase and filter out invalid characters
    subjectcode = input.value.toUpperCase().replace(/[^A-Z0-9]/g, '');
    // Set the filtered value back to the input
    input.value = subjectcode;
  }

  function preventSymbols(event) {
    const key = event.key;
    // Allow letters, numbers, and spaces
    if (/[^A-Za-z0-9\s]/.test(key)) {
      event.preventDefault();  // Prevent the default action for symbols
    }
  }
   
</script>

<h4 class="text-center">Add Subjects</h4>

<div class="row justify-content-center align-items-center h-100">
    <div class="col-4">
        <div class="card shadow-2-strong card-registration" style="border-radius: 15px;">
            <div class="card-body p-4 p-md-5">
                <!-- <h3 class="mb-4 pb-2 pb-md-0 mb-md-5">Add Account Details Form</h3> -->


<form class="form-inline" on:submit|preventDefault={handleSubmit}>

   
   

    <div class="row">
        <div class="col-md-12 mb-4">
            <div data-mdb-input-init class="form-outline">
        <label class="form-label" for="subjectcode">Subject Code:</label>
        <input class="form-control form-control-md" placeholder="subject code" id="subjectcode" type="text" on:input={filterInput}   bind:value={subjectcode} required />
    </div>
    </div>
    </div>

    <div class="row">
        <div class="col-md-12 mb-4">
            <div data-mdb-input-init class="form-outline">
        <label class="form-label" for="title">Subject Title:</label>
        <input class="form-control form-control-md" placeholder="Subject Title" id="title" type="text" on:keypress={preventSymbols}  bind:value={title} required />
    </div>
    </div>
    </div>
  

    <div class="mt-4 pt-2">
        <button data-mdb-ripple-init class="btn btn-primary btn-lg" type="submit">ADD SUBJECT</button>
      </div>
   
</form>












         
</div>

</div>

</div>

</div>


{#if message}
    <p>{message}</p>
{/if}




<!-- 
<div class="row">

    <div class="col-md-6 mb-4"> 
        <div data-mdb-input-init class="form-outline">
            <label class="form-label" for="firstName" >First Name:</label>
            <input type="text" class="form-control form-control-md" placeholder="First Name" style="position:relative;"  bind:value={firstName} required />
        </div></div>    
             
<div class="col-md-6 mb-4">
    <div data-mdb-input-init class="form-outline">
        <label class="form-label" for="lastName">Last Name:</label>
    <input id="lastName" type="text" class="form-control form-control-md" placeholder="Last Name" bind:value={lastName} required />
    </div></div>

</div>  

<div class="row">
    <div class="col-md-6 mb-4">
        <div data-mdb-input-init class="form-outline">
        <label class="form-label" for="password">Password:</label>
        <input class="form-control form-control-md" placeholder="password" id="password" type="password" bind:value={password} required />
    </div></div>
    
    
    <div class="col-md-6 mb-4">
    <div data-mdb-input-init class="form-outline">
    <label class="form-label" for="confirmPassword">Confirm Password:</label>
    <input class="form-control form-control-md" placeholder="ConfirmPassword" id="confirmPassword" type="password" bind:value={confirmPassword} required />
    </div></div>
    
    
    </div>
    
    
    
    <div class="row">
    <div class="col-10 ">
        <label class="form-label select-label" for="role">Role:</label>
        <select class="form-select" id="role"  bind:value={role}>
            {#each roles as r}
                <option value={r}>{r}</option>
            {/each}
        </select>
    </div>
    </div>
     -->




<style>
 
 input:focus::placeholder {
  color: transparent;
}

</style>