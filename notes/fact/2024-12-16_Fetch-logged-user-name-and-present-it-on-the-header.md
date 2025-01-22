---
date: 2024-12-16
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Fetch logged user name and present it on the header

In addition to [[2024-12-16_fetch-books-to-user-state|books data]], the user name is also one of the data that needs to be fetched when the user logs in. This data needs to be displayed on the header so that the user knows which account they are currently logged in with.

Instead of auth.users table, we fetch the user name from the public.user_names table which we created in [[2024-12-14_Create-a-public-table-on-Supabase-to-track-auth-users-table|this topic]] because public schema is easier to query from the client side.

```ts
// src/lib/components/state/user-state.svelte.ts

import type { Database } from "$lib/types/database.types";
import type { User, Session, SupabaseClient } from "@supabase/supabase-js";
import { getContext, setContext } from "svelte";

interface UserStateProps {
  session: Session | null;
  supabase: SupabaseClient<Database> | null;
  user: User | null;
}

interface Book {
  author: string | null
  cover_image: string | null
  created_at: string
  description: string | null
  finished_reading_on: string | null
  genre: string | null
  id: number
  rating: number | null
  started_reading_on: string | null
  title: string
  user_id: string
}

export class UserState {
  session = $state<Session | null>(null);
  supabase = $state<SupabaseClient | null>(null);
  user = $state<User | null>(null);
  userName = $state<string | null>(null);  // new state to store user name, default null for unauthenticated users
  books = $state<Book[]>([]);

  constructor(data: UserStateProps) {
    this.updateState(data);
  }

  updateState({ session, supabase, user }: UserStateProps) {
    this.session = session;
    this.supabase = supabase;
    this.user = user;

    this.fetchUserData();
  }

  async fetchUserData() {
    if (!this.user || !this.supabase) return;

    // const { data, error } = await this.supabase.from("books").select("*").eq("user_id", this.user.id);  before we only fetched books

    // now, we fetch user name as well, these two calls can be done in parallel because they are independent to each other
    const [
      fetchBooksResponse,
      fetchUserNameResponse
    ] = await Promise.all([
      this.supabase.from("books").select("*").eq("user_id", this.user.id),
      this.supabase.from("user_names").select("name").eq("user_id", this.user.id).single()
                                                                              // ^^^^^^^^^ because user name should be unique, so better add single() to make sure only one row is returned
    ])
    // destructuring the response to get data and error from both fetches
    const { data: books, error: booksError } = fetchBooksResponse;
    const { data: userName, error: userNamesError } = fetchUserNameResponse;
    if (booksError || userNamesError || !books || !userName) { // error condition is changed to include both books and userNamesError now
      console.error("Error fetching user data", {
        booksError,
        userNamesError,
      });
      return
    }
    this.books = books;
    this.userName = userName.name; // store fetched user name in the state
  }

  async logout() {
    await this.supabase?.auth.signOut();
  }
}

const USER_STATE_KEY = Symbol("USER_STATE");

export function setUserState(data: UserStateProps) {
  return setContext(USER_STATE_KEY, new UserState(data));
}

export function getUserState() {
  return getContext<ReturnType<typeof setUserState>>(USER_STATE_KEY);
}

```

Like books, now the user name is also fetched and stored in the user state when user logs in. It can be easily accessed from the header component to display the user name.

```svelte
<!-- src/lib/components/Header.svelte  -->

<script lang="ts">
  import bookNextLogo from '$assets/app-logo.svg';
  import { Button } from '$components';
  import { getUserState } from './state/user-state.svelte';

  let userState = getUserState()
  let { userName } = $derived(userState)
  //    ^^^^^^^^ before is `user`, now we replace it with `userName`

  function onclick() {
    userState.logout()
  }
</script>

<header>
  <a href="/">
    <img class="logo " src={bookNextLogo} alt="Go to home">
  </a>
  <nav>
    {#if userName}
    <!-- ^^^^^^^^ replace user with userName, which is totally fine because when user is not logged in, userName is null -->
      <ul>
        <li>
          <p>{userName}</p>
         <!-- ^^^^^^^^ before we used `user.email` for dummy data here, now we replace it with `userName` to meet the real requirement -->
        </li>
        <li>
          <Button isMenu={true} {onclick}>Logout</Button>
        </li>
      </ul>
    {:else}
      <ul>
        <li>
          <Button isMenu href="/register">Create account</Button>
        </li>
        <li>
          <Button isMenu isSecondary href="/login">Login</Button>
        </li>
      </ul>
    {/if}
  </nav>
</header>

<style>
  header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 12px 4vw;
  }
  ul {
    display: flex;
    gap: 24px;
    align-items: center;
  }
  .logo {
    height: 72px;
  }
</style>

```
