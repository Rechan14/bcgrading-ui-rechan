<script>
    import 'bootstrap/dist/css/bootstrap.min.css';
    import { onMount } from 'svelte';
    import { goto } from '$app/navigation';
    import { fade } from 'svelte/transition';
    import {jwtDecode} from 'jwt-decode';

    let years = [];
    let semesters = [];
    let subjects = [];
    let classes = [];
    let error = '';
    let showUnauthorizedMessage = false;
    let countdown = 5;
    let redirectMessage = '';
    let userRole = '';
    let showLogoutConfirm = false;

   

    onMount(async () => {

       // Import Bootstrap JS
       await import('bootstrap/dist/js/bootstrap.bundle.min.js');

        const token = localStorage.getItem('jwtToken');

        
        if (!token) {
            // No token found, redirect to login page
            unauthorizedAccess("No token found, redirecting to login.");
            return;
        }

        try {
            const decodedToken = jwtDecode(token);
            userRole = decodedToken.role;  // Save userRole for later use

            // Check if the user has the correct role to access the page
            if (userRole !== 'Registrar') {
                // Unauthorized access, redirect to the page for the current user role
                redirectMessage = `Role '${userRole}' does not have access to this page.`;
                unauthorizedAccess("Redirecting you to your role-specific page.");
                return;
            }


          


            // Fetch the years if the user is authorized
            const yearlist = await fetch('http://localhost:4000/registrar/years', {
                headers: {
                    'Authorization': `Bearer ${token}` // Include JWT token
                }
            });

            if (yearlist.ok) {
                years = await yearlist.json();
            } else {
                error = `Failed to fetch years: ${yearlist.statusText}`;
            }


            const semesterlist = await fetch('http://localhost:4000/registrar/semesters', {
                headers: {
                    'Authorization': `Bearer ${token}` // Include JWT token
                }
            });

            if (semesterlist.ok) {
                semesters = await semesterlist.json();
            } else {
                error = `Failed to fetch semesters: ${semesterlist.statusText}`;
            }
            

            const subjectlist = await fetch('http://localhost:4000/registrar/subjects', {
                headers: {
                    'Authorization': `Bearer ${token}` // Include JWT token
                }
            });

            if (subjectlist.ok) {
                subjects = await subjectlist.json();
            } else {
                error = `Failed to fetch subjects: ${subjectlist.statusText}`;
            }

            
            const classlist = await fetch('http://localhost:4000/registrar', {
                headers: {
                    'Authorization': `Bearer ${token}` // Include JWT token
                }
            });

            if (classlist.ok) {
                classes = await classlist.json();
            } else {
                error = `Failed to fetch classLIST: ${classlist.statusText}`;
            }

           

        
        } catch (error) {
            console.error('Error:', error); // Log the entire error object

            console.log(userRole)
            unauthorizedAccess("Error decoding token, redirecting to login.");
        }

           
    });

    let filteredClasses = [];
   
    let selectedYear = '';
    let selectedSemester = '';
    let selectedSubject = '';

    function filterClasses() {
    console.log('Selected Year:', selectedYear);
    console.log('Selected Semester:', selectedSemester);
    console.log('Selected Subject:', selectedSubject);

    if (selectedYear === '' && selectedSemester === '' && selectedSubject === '') {
        filteredClasses = [...classes];
    } else {
        filteredClasses = classes.filter(cls => {
            return (
                (selectedYear === '' || cls.year === selectedYear) &&
                (selectedSemester === '' || cls.semester === selectedSemester) &&
                (selectedSubject === '' || cls.subjectcode === selectedSubject)
            );
        });
    }

    console.log('Filtered Classes:', filteredClasses);
}


    function handleYearSelect(year) {
        console.log('Year selected:', year);
        selectedYear = year;
        filterClasses();
    }

    function handleSemesterSelect(semester) {
        console.log('Semester selected:', semester);
        selectedSemester = semester;
        filterClasses();
    }

    function handleSubjectSelect(subject) {
        console.log('Subject selected:', subject);
        selectedSubject = subject;
        filterClasses();
    }


    function unauthorizedAccess(message) {
        console.error(message); // Debugging line
        showUnauthorizedMessage = true;
        redirectMessage = message;
        const interval = setInterval(() => {
            countdown--;
            if (countdown <= 0) {
                clearInterval(interval);
                if (redirectMessage.includes("login")) {
                    goto('/login');  // Redirect to login page
                } else {
                    goto(`/${userRole}`);  // Redirect to User Role
                }
            }
        }, 1000);
    }

    function clearFilters() {
    selectedYear = '';
    selectedSemester = '';
    selectedSubject = '';
    filterClasses();
}



</script>

<div class="container text-center">
    <div class="btn-group me-2">
        <button type="button" style="width: 160px !important;" class="btn dropdown-toggle border-dark" data-bs-toggle="dropdown" aria-expanded="false">
        YEAR:  {selectedYear}            
        </button>
        <ul class="dropdown-menu" style="max-height: 200px; overflow-y: auto;">
            {#each years as year}
            <li class="bg-light border"><button class="dropdown-item" on:click={() => handleYearSelect(year.year)}>{year.year}</button></li>
            {/each}
        </ul>
    </div>

    <div class="btn-group me-2">
        <button type="button" style="width: 190px !important;" class="btn dropdown-toggle border-dark" data-bs-toggle="dropdown">
        SEMESTER:  {selectedSemester}          
        </button>
        <ul class="dropdown-menu" style="max-height: 200px; overflow-y: auto; width: 190px !important;">
            {#each semesters as semester}
            <li class="bg-light border">
                <button class="dropdown-item" on:click={() => handleSemesterSelect(semester.semester)}>{semester.semester}</button>
            </li>
            {/each}
        </ul>
    </div>

    <div class="btn-group">
        <button type="button" style="width: 500px !important;" class="btn dropdown-toggle border-dark" data-bs-toggle="dropdown">
         SUBJECT: {selectedSubject}           
        </button>
        <ul class="dropdown-menu" style="max-height: 200px; overflow-y: auto; width: 500px !important;">
            {#each subjects as subject}
            <li class="bg-light border">
                <button class="dropdown-item" on:click={() => handleSubjectSelect(subject.subjectcode)}>{subject.subjectcode} - {subject.title}</button>
            </li>
            {/each}
        </ul>
    </div>
    <button type="button" class="btn text-white btn-dark" on:click={clearFilters}>Clear Filters <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-x-lg" viewBox="0 0 16 16">
        <path d="M2.146 2.854a.5.5 0 1 1 .708-.708L8 7.293l5.146-5.147a.5.5 0 0 1 .708.708L8.707 8l5.147 5.146a.5.5 0 0 1-.708.708L8 8.707l-5.146 5.147a.5.5 0 0 1-.708-.708L7.293 8z"/>
      </svg></button>
</div>






<h2>Class List</h2>



<!-- {#if !error} -->
{#if !error}
    {#if selectedYear === '' && selectedSemester === '' && selectedSubject === ''}
        <!-- Show entire table if no filters are selected -->
        <div style="max-height: 73vh; overflow-y: auto;">
        <table class="table table-bordered" style="width: 100% !important;">
            <thead>
                <tr>
                    <th>Class ID</th>
                    <th>Subject Code</th>
                    <th>Subject Title</th>
                    <th>Year</th>
                    <th>Semester</th>
                    <th>Teacher ID</th>
                    <th>Created</th>
                    <th>Updated</th>
                    <th>Status</th>
                </tr>
            </thead>
            <tbody>
                {#each classes as classlist}
                <tr>
                    <td>{classlist.classid}</td>
                    <td>{classlist.subjectcode}</td>
                    <td>{classlist.title}</td>
                    <td>{classlist.year}</td>
                    <td>{classlist.semester}</td>
                    <td>{classlist.teacherid}</td>
                    <td>{classlist.created}</td>
                    <td>{classlist.updated}</td>
                    <td>
                        {#if classlist.isActive}
                            <p class="badge-lg text-center text-bg-success">Active</p>
                        {:else}
                            <p class="badge-lg text-center text-bg-danger">Inactive</p>
                        {/if}
                    </td>
                </tr>
                {/each}
            </tbody>
        </table>
        </div>
    {:else}
        {#if filteredClasses.length > 0}
            <!-- Show filtered table if there are results -->
            <div style="max-height: 73vh; overflow-y: auto;">
                <table class="table table-bordered" style="width: 100% !important;">
                <thead>
                    <tr>
                        <th>Class ID</th>
                        <th>Subject Code</th>
                        <th>Subject Title</th>
                        <th>Year</th>
                        <th>Semester</th>
                        <th>Teacher ID</th>
                        <th>Created</th>
                        <th>Updated</th>
                        <th>Status</th>
                    </tr>
                </thead>
                <tbody>
                    {#each filteredClasses as classlist}
                    <tr>
                        <td>{classlist.classid}</td>
                        <td>{classlist.subjectcode}</td>
                        <td>{classlist.title}</td>
                        <td>{classlist.year}</td>
                        <td>{classlist.semester}</td>
                        <td>{classlist.teacherid}</td>
                        <td>{classlist.created}</td>
                        <td>{classlist.updated}</td>
                        <td>
                            {#if classlist.isActive}
                                <p class="badge-lg text-center text-bg-success">Active</p>
                            {:else}
                                <p class="badge-lg text-center text-bg-danger">Inactive</p>
                            {/if}
                        </td>
                    </tr>
                    {/each}
                </tbody>
            </table>
            </div>
        {:else}
            <h1 class="text-center">No classes found </h1>
        {/if}
    {/if}
{/if}

<!-- {/if} -->




<!-- <td>
    {#if classlist.isActive}
        <p class="badge-lg text-center text-bg-success">Active</p>
    {:else}
        <p class="badge-lg text-center text-bg-danger">Inactive</p>
    {/if}
</td> -->


{#if showUnauthorizedMessage}
<div class="popup" in:fade>
    <div class="popup-content">
        <p>{redirectMessage}</p>
        <p>Redirecting you in {countdown} seconds...</p>
    </div>
</div>
{/if}


{#if error}
    <p>{error}</p>
{/if}

<style>
    .popup {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background-color: rgba(0, 0, 0, 0.9);
        color: white;
        display: flex;
        justify-content: center;
        align-items: center;
        z-index: 1000;
    }

    .popup-content {
        padding: 20px;
        background-color: rgba(255, 255, 255, 0.1);
        border-radius: 8px;
        text-align: center;
    }
   
</style>
