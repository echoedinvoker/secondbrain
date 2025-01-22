---
date: 2024-12-08
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Styling the AuthForm of Book Next

After creating [[2024-12-08_Create-structure-of-register-and-login-pages-of-Book-Nest|the structure of register and login pages of Book Nest]], we can now start to style it.

```svelte
<!-- src/lib/components/AuthForm.svelte  -->

<script lang="ts">
    import { Button } from "$components";

  interface AuthFormProps {
    isRegister?: boolean; 
  }

  let { isRegister }: AuthFormProps = $props();
</script>

<div class="auth-container default-margin">
                      <!-- ^^^^^^^^^^^^^^ -->
  <h1 class="mb-l">{isRegister ? "Register" : "Login"}</h1>
 <!-- ^^^^^^^^^^^^ -->
  <div class="form-and-social-login">
    <form class="auth-form">
      {#if isRegister}
        <input type="text" name="name" placeholder="Name" />
      {/if}
      <input type="text" name="email" placeholder="Email" />
      <input type="password" name="password" placeholder="Password" />
      {#if isRegister}
        <input type="password" name="passwordConfirm" placeholder="Confirm Password" />
      {/if}
      <Button type="submit">{isRegister ? "Register" : "Login"}</Button>
      {#if isRegister}
        <p class="auth-hint mt-s">Already have an account? <a href="/login">Login</a></p>
                       <!-- ^^^^ -->
      {:else}
        <p class="auth-hint mt-s">Don't have an account? <a href="/register">Register</a></p>
                       <!-- ^^^^ -->
      {/if}
    </form>
    <div class="social-login">
    </div>
  </div>
</div>

<style>
  .auth-container {
    margin-top: 80px;
  }

  .form-and-social-login {
    display: flex;
  }

  .auth-form {
    display: flex;
    flex-direction: column;
    align-items: start;
    border-right: 1px solid grey;
    padding-right: 80px;  /* to make some space between input right edge and right border line */
    width: 40%;
  }

  .auth-form input {
    width: 100%; /* to make all inputs have the same width */
    margin-bottom: 12px; /* because flex items include inputs and button, so `gap` property doesn't work */
                         /* but we still can use `margin-bottom` to create space between inputs */
  }

  /* If using `margin` to create space between, we should remove the margin of the last item */
  .auth-form input:last-of-type {
    margin-bottom: 30px;
  }

  .auth-hint {
    font-size: 16px;
    color: grey;
  }

  /* auth error will be added in future, below we just add the style of it in advance */
  .auth-error {
    background-color: rgb(122, 35, 35);
    color: white;
    font-size: 18px;
    border-radius: 12px;
    padding: 12px;
    width: 100%;
    margin-bottom: 8px; /* auth errors can be multiple, so we should add margin between */
  }
  .auth-error:last-of-type  {
    margin-bottom: 16px; /* same, if we use `margin` to create space between, we should remove the margin of the last item */
  }

  .social-login {
    padding-left: 80px;
    width: 40%;
  }
</style>

```
