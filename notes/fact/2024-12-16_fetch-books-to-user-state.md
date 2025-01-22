---
date: 2024-12-16
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# fetch books to user state

We should fetch the data belonging to the user when the user logs in. We write the operation of fetching user data and the fetched data into the `UserState` also.

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

// copied from types/database.types.ts to save time
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
  books = $state<Book[]>([]); // new state to store fetched books data

  constructor(data: UserStateProps) {
    this.updateState(data);
  }

  updateState({ session, supabase, user }: UserStateProps) {
    this.session = session;
    this.supabase = supabase;
    this.user = user;

    this.fetchUserData();  // whenever user is updated, fetch user data
  }

  // fetch user data from supabase
  async fetchUserData() {
    if (!this.user || !this.supabase) return; // if user or supabase is not available (not login yet), not to do anything

    // get user data from supabase (filter by user_id), now only books data needed to be fetched
    const { data, error } = await this.supabase.from("books").select("*").eq("user_id", this.user.id);
    if (error) {
      console.error("Error fetching user data", error);
      return
    }
    this.books = data; // update books state with fetched data
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

The login form action we previously wrote will redirect the user to /private/dashboard after a successful login, so we can check if the fetched books data is correct on that page.

```bash
~/D/g/s/s/book-nest > tree src/routes/
src/routes/
├── auth
│   └── callback
│       └── +server.ts
├── +layout.server.ts
├── +layout.svelte
├── +layout.ts
├── login
│   ├── +page.server.ts
│   └── +page.svelte
├── logout
│   ├── +page.server.ts
│   └── +page.svelte
├── +page.svelte
├── private # new route branch for logged in user only
│   └── dashboard
│       └── +page.svelte  # home page for logged in user, there will be a list of books
└── register
    ├── +page.server.ts
    └── +page.svelte
```
```svelte
~/D/g/s/s/book-nest > cat src/routes/private/dashboard/+page.svelte 
<script lang="ts">
  import { getUserState } from "$components/state/user-state.svelte";

  let userState = getUserState()
  let { books } = $derived(userState)  // in user state, books data should be ready to use for logged in user
                                       // we can directly use it in the template

  $inspect(books)  // in this stage, we only need to check if books data is correct
</script>
```
```bash
~/D/g/s/s/book-nest > npm run dev  # check books data in the browser
[
    {
        "id": 1,
        "created_at": "2024-12-15T07:13:17.961927+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "The Defense",
        "description": null,
        "rating": 4,
        "started_reading_on": "2023-05-10",
        "finished_reading_on": "2023-06-15",
        "author": "Steve Cavanagh",
        "genre": "Legal Thriller",
        "cover_image": "https://m.media-amazon.com/images/I/71uLZq+Eg1L._AC_UF894,1000_QL80_.jpg"
    },
    {
        "id": 2,
        "created_at": "2023-04-15T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "Twisted",
        "description": "A novel with unexpected twists.",
        "rating": 4,
        "started_reading_on": "2023-03-01",
        "finished_reading_on": "2023-07-22",
        "author": "Steve Cavanagh",
        "genre": "Psychological Thriller",
        "cover_image": null
    },
    {
        "id": 3,
        "created_at": "2023-03-15T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "Rules of Prey",
        "description": "The first book in the Lucas Davenport series.",
        "rating": 3,
        "started_reading_on": "2023-05-10",
        "finished_reading_on": "2023-05-10",
        "author": "John Sandford",
        "genre": "Crime Thriller",
        "cover_image": null
    },
    {
        "id": 4,
        "created_at": "2024-12-15T07:13:17.961927+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "Night Prey",
        "description": null,
        "rating": 4,
        "started_reading_on": "2024-01-08",
        "finished_reading_on": "2024-03-01",
        "author": "John Sandford",
        "genre": "Crime Thriller",
        "cover_image": null
    },
    {
        "id": 5,
        "created_at": "2024-06-15T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "Secret Prey",
        "description": null,
        "rating": null,
        "started_reading_on": null,
        "finished_reading_on": null,
        "author": "John Sandford",
        "genre": "Crime Thriller",
        "cover_image": null
    },
    {
        "id": 6,
        "created_at": "2023-09-15T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "The Lincoln Lawyer",
        "description": "A legal thriller featuring Mickey Haller.",
        "rating": 4,
        "started_reading_on": "2024-05-31",
        "finished_reading_on": "2024-06-12",
        "author": "Michael Connelly",
        "genre": "Legal Thriller",
        "cover_image": null
    },
    {
        "id": 7,
        "created_at": "2024-08-04T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "The Reversal",
        "description": null,
        "rating": null,
        "started_reading_on": null,
        "finished_reading_on": null,
        "author": "Michael Connelly",
        "genre": "Legal Thriller",
        "cover_image": null
    },
    {
        "id": 8,
        "created_at": "2024-08-10T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "The Gods of Guilt",
        "description": null,
        "rating": null,
        "started_reading_on": null,
        "finished_reading_on": null,
        "author": "Michael Connelly",
        "genre": "Legal Thriller",
        "cover_image": null
    },
    {
        "id": 9,
        "created_at": "2024-04-21T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "The Poet",
        "description": null,
        "rating": 4,
        "started_reading_on": "2022-04-01",
        "finished_reading_on": "2022-07-25",
        "author": "Michael Connelly",
        "genre": "Crime Thriller",
        "cover_image": null
    },
    {
        "id": 10,
        "created_at": "2024-12-15T07:13:17.961927+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "Void Moon",
        "description": "A standalone novel by Michael Connelly.",
        "rating": null,
        "started_reading_on": null,
        "finished_reading_on": null,
        "author": "Michael Connelly",
        "genre": "Crime Thriller",
        "cover_image": null
    },
    {
        "id": 11,
        "created_at": "2024-05-17T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "The Road",
        "description": "A harrowing journey of a father and son through a desolate, post-apocalyptic landscape.",
        "rating": 4,
        "started_reading_on": "2024-07-23",
        "finished_reading_on": "2024-08-05",
        "author": "Cormac McCarthy",
        "genre": "Post-Apocalyptic Fiction",
        "cover_image": "https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.amazon.com%2FRoad-Cormac-McCarthy%2Fdp%2F0307387895&psig=AOvVaw3MTDY-nZEEuEHzPqw1Uv1e&ust=1728238226262000&source=images&cd=vfe&opi=89978449&ved=0CBQQjRxqFwoTCPCzlp7r94gDFQAAAAAdAAAAABAE"
    },
    {
        "id": 12,
        "created_at": "2024-01-08T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "The Girl with the Dragon Tattoo",
        "description": "A gripping crime novel that introduced the world to the character Lisbeth Salander.",
        "rating": 4,
        "started_reading_on": "2018-03-10",
        "finished_reading_on": "2018-06-18",
        "author": "Stieg Larsson",
        "genre": "Crime, Thriller",
        "cover_image": null
    },
    {
        "id": 13,
        "created_at": "2024-05-31T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "Gone Girl",
        "description": "A twisted psychological thriller about a marriage gone wrong.",
        "rating": null,
        "started_reading_on": null,
        "finished_reading_on": null,
        "author": "Gillian Flynn",
        "genre": "Psychological Thriller",
        "cover_image": null
    },
    {
        "id": 14,
        "created_at": "2022-11-29T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "Harry Potter and the Deathly Hallows",
        "description": null,
        "rating": 5,
        "started_reading_on": "2016-02-09",
        "finished_reading_on": "2016-08-05",
        "author": "J.K. Rowling",
        "genre": "Fantasy",
        "cover_image": "https://static.posters.cz/image/1300/art-photo/harry-potter-deathly-hallows-book-cover-i214933.jpg"
    },
    {
        "id": 15,
        "created_at": "2024-09-03T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "The Hunger Games",
        "description": "A dystopian novel about a televised fight to the death in a post-apocalyptic world.",
        "rating": null,
        "started_reading_on": null,
        "finished_reading_on": null,
        "author": "Suzanne Collins",
        "genre": "Dystopian",
        "cover_image": null
    },
    {
        "id": 16,
        "created_at": "2024-10-06T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "A Thousand Splendid Suns",
        "description": "A heart-wrenching story of two women in Afghanistan.",
        "rating": null,
        "started_reading_on": null,
        "finished_reading_on": null,
        "author": "Khaled Hosseini",
        "genre": "Historical Fiction",
        "cover_image": null
    },
    {
        "id": 17,
        "created_at": "2024-06-13T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "The Book Thief",
        "description": "A novel about a young girl in Nazi Germany who finds solace by stealing books.",
        "rating": null,
        "started_reading_on": null,
        "finished_reading_on": null,
        "author": "Markus Zusak",
        "genre": "Historical Fiction",
        "cover_image": "https://m.media-amazon.com/images/I/71H52+sSb4L._AC_UF894,1000_QL80_.jpg"
    },
    {
        "id": 18,
        "created_at": "2024-12-15T07:13:17.961927+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "The Fault in Our Stars",
        "description": "A poignant love story between two teenagers with cancer.",
        "rating": 4,
        "started_reading_on": "2020-03-28",
        "finished_reading_on": "2020-06-08",
        "author": "John Green",
        "genre": "Young Adult",
        "cover_image": null
    },
    {
        "id": 19,
        "created_at": "2024-09-28T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "The Goldfinch",
        "description": "A Pulitzer Prize-winning novel about a boy whose life is shaped by tragedy and art.",
        "rating": 5,
        "started_reading_on": "2023-10-21",
        "finished_reading_on": "2023-12-10",
        "author": "Donna Tartt",
        "genre": "Literary Fiction",
        "cover_image": null
    },
    {
        "id": 20,
        "created_at": "2024-09-13T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "The Night Circus",
        "description": "A magical competition between two young illusionists.",
        "rating": 4,
        "started_reading_on": null,
        "finished_reading_on": "2023-07-30",
        "author": "Erin Morgenstern",
        "genre": "Fantasy",
        "cover_image": "https://m.media-amazon.com/images/I/71+whvJjE3L._AC_UF894,1000_QL80_.jpg"
    },
    {
        "id": 21,
        "created_at": "2024-09-13T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "The Girl on the Train",
        "description": "A psychological thriller about a woman who becomes obsessed with a couple she sees from the train.",
        "rating": 4,
        "started_reading_on": "2020-09-19",
        "finished_reading_on": "2020-11-22",
        "author": "Paula Hawkins",
        "genre": "Psychological Thriller",
        "cover_image": null
    },
    {
        "id": 22,
        "created_at": "2024-09-13T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "The Help",
        "description": "A story about African American maids working in white households in the South during the 1960s.",
        "rating": 5,
        "started_reading_on": "2023-11-09",
        "finished_reading_on": "2023-12-05",
        "author": "Kathryn Stockett",
        "genre": "Historical Fiction",
        "cover_image": null
    },
    {
        "id": 23,
        "created_at": "2024-09-13T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "Life of Pi",
        "description": "A fantastical journey of a young boy stranded on a lifeboat with a Bengal tiger.",
        "rating": 4,
        "started_reading_on": "2022-09-11",
        "finished_reading_on": "2022-11-15",
        "author": "Yann Martel",
        "genre": "Adventure, Drama",
        "cover_image": "https://m.media-amazon.com/images/I/816NlEQFMOL._AC_UF894,1000_QL80_.jpg"
    },
    {
        "id": 24,
        "created_at": "2024-09-13T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "American Gods",
        "description": null,
        "rating": 4,
        "started_reading_on": "2019-01-31",
        "finished_reading_on": "2019-02-27",
        "author": "Neil Gaiman",
        "genre": "Fantasy",
        "cover_image": "https://m.media-amazon.com/images/I/716LpMKQ3iL._AC_UF894,1000_QL80_.jpg"
    },
    {
        "id": 25,
        "created_at": "2024-10-05T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "The Da Vinci Code",
        "description": null,
        "rating": 4,
        "started_reading_on": "2024-08-20",
        "finished_reading_on": "2024-09-01",
        "author": "Dan Brown",
        "genre": "Mystery, Thriller",
        "cover_image": "https://upload.wikimedia.org/wikipedia/en/thumb/6/6b/DaVinciCode.jpg/220px-DaVinciCode.jpg"
    },
    {
        "id": 26,
        "created_at": "2024-10-05T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "The Lovely Bones",
        "description": "A haunting tale of a young girl watching her family from the afterlife after her murder.",
        "rating": 4,
        "started_reading_on": "2022-01-13",
        "finished_reading_on": "2022-02-18",
        "author": "Alice Sebold",
        "genre": "Drama, Crime",
        "cover_image": null
    },
    {
        "id": 27,
        "created_at": "2024-10-05T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "The Alchemist",
        "description": "A mystical story about Santiago, an Andalusian shepherd boy who yearns to travel in search of a worldly treasure.",
        "rating": 5,
        "started_reading_on": "2022-12-28",
        "finished_reading_on": "2023-01-15",
        "author": "Paulo Coelho",
        "genre": "Adventure, Fantasy",
        "cover_image": "https://images-na.ssl-images-amazon.com/images/S/compressed.photo.goodreads.com/books/1654371463i/18144590.jpg"
    },
    {
        "id": 28,
        "created_at": "2024-10-05T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "The Casual Vacancy",
        "description": "A dark and humorous drama set in a small English town.",
        "rating": 3,
        "started_reading_on": "2021-07-01",
        "finished_reading_on": "2021-09-20",
        "author": "J.K. Rowling",
        "genre": "Drama",
        "cover_image": null
    },
    {
        "id": 29,
        "created_at": "2024-10-05T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "Twilight",
        "description": "A teenage girl falls in love with a vampire.",
        "rating": 2,
        "started_reading_on": "2023-05-28",
        "finished_reading_on": "2023-06-10",
        "author": "Stephenie Meyer",
        "genre": "Fantasy, Romance",
        "cover_image": null
    },
    {
        "id": 30,
        "created_at": "2024-10-05T00:00:00+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "Fifty Shades Darker",
        "description": null,
        "rating": null,
        "started_reading_on": "2022-06-06",
        "finished_reading_on": null,
        "author": "E.L. James",
        "genre": "Erotic Romance",
        "cover_image": null
    },
    {
        "id": 31,
        "created_at": "2024-12-15T07:13:17.961927+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "The Girl Who Played with Fire",
        "description": "The second book in the Millennium series.",
        "rating": 4,
        "started_reading_on": "2020-10-02",
        "finished_reading_on": "2020-11-05",
        "author": "Stieg Larsson",
        "genre": "Crime, Thriller",
        "cover_image": null
    },
    {
        "id": 32,
        "created_at": "2024-12-15T07:13:17.961927+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "Breaking Dawn",
        "description": null,
        "rating": null,
        "started_reading_on": null,
        "finished_reading_on": null,
        "author": "Stephenie Meyer",
        "genre": "Fantasy, Romance",
        "cover_image": null
    },
    {
        "id": 33,
        "created_at": "2024-12-15T07:13:17.961927+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "The Host",
        "description": "An alien race takes over the minds of humans.",
        "rating": 3,
        "started_reading_on": "2022-02-10",
        "finished_reading_on": "2022-10-28",
        "author": "Stephenie Meyer",
        "genre": "Science Fiction, Romance",
        "cover_image": null
    },
    {
        "id": 34,
        "created_at": "2024-12-15T07:13:17.961927+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "Mockingjay",
        "description": "The final book in The Hunger Games trilogy.",
        "rating": 4,
        "started_reading_on": "2022-11-12",
        "finished_reading_on": "2023-08-10",
        "author": "Suzanne Collins",
        "genre": "Dystopian",
        "cover_image": null
    },
    {
        "id": 35,
        "created_at": "2024-12-15T07:13:17.961927+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "Eragon",
        "description": "A young farm boy discovers his destiny as a dragon rider.",
        "rating": 3,
        "started_reading_on": "2023-05-31",
        "finished_reading_on": "2023-07-30",
        "author": "Christopher Paolini",
        "genre": "Fantasy",
        "cover_image": null
    },
    {
        "id": 36,
        "created_at": "2024-12-15T07:13:17.961927+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "Allegiant",
        "description": "The conclusion to the Divergent trilogy.",
        "rating": 3,
        "started_reading_on": "2022-12-31",
        "finished_reading_on": "2023-07-12",
        "author": "Veronica Roth",
        "genre": "Dystopian",
        "cover_image": null
    },
    {
        "id": 37,
        "created_at": "2024-12-15T07:13:17.961927+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "The Maze Runner",
        "description": "A group of teenagers must navigate a deadly maze.",
        "rating": 3,
        "started_reading_on": "2020-01-18",
        "finished_reading_on": "2020-06-18",
        "author": "James Dashner",
        "genre": "Dystopian, Science Fiction",
        "cover_image": "https://m.media-amazon.com/images/I/61x7JsmTRrL._AC_UF894,1000_QL80_.jpg"
    },
    {
        "id": 38,
        "created_at": "2024-12-15T07:13:17.961927+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "The Lost Symbol",
        "description": "A symbologist is drawn into a mystery involving the Freemasons.",
        "rating": 3,
        "started_reading_on": "2024-05-18",
        "finished_reading_on": "2024-07-25",
        "author": "Dan Brown",
        "genre": "Thriller, Mystery",
        "cover_image": "https://m.media-amazon.com/images/I/81BlXbpNmuL._AC_UF894,1000_QL80_.jpg"
    },
    {
        "id": 39,
        "created_at": "2024-12-15T07:13:17.961927+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "The Girl on the Cliff",
        "description": null,
        "rating": null,
        "started_reading_on": null,
        "finished_reading_on": null,
        "author": "Lucinda Riley",
        "genre": "Historical Fiction",
        "cover_image": null
    },
    {
        "id": 40,
        "created_at": "2024-12-15T07:13:17.961927+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "Divergent",
        "description": "A young girl must navigate a society divided into factions.",
        "rating": 4,
        "started_reading_on": "2023-09-10",
        "finished_reading_on": "2023-10-20",
        "author": "Veronica Roth",
        "genre": "Dystopian, Science Fiction",
        "cover_image": null
    },
    {
        "id": 41,
        "created_at": "2024-12-15T07:13:17.961927+00:00",
        "user_id": "319f7004-b5ee-4a8f-8ff2-09d62dd41842",
        "title": "The Kite Runner",
        "description": "A story of friendship and redemption set in Afghanistan.",
        "rating": null,
        "started_reading_on": "2024-09-30",
        "finished_reading_on": null,
        "author": "Khaled Hosseini",
        "genre": "Historical Fiction",
        "cover_image": "https://m.media-amazon.com/images/I/81CA-WqU+lL._AC_UF894,1000_QL80_.jpg"
    }
]
```
```
