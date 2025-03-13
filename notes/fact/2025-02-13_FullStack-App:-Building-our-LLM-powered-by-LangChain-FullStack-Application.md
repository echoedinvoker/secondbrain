---
date: 2025-02-13
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# FullStack App: Building our LLM powered by LangChain FullStack Application

For this topic, we need to use [[2025-02-13_Output-Parsers:-Getting-Ready-to-work-with-a-Frontend|the parts we have already completed]] to finish a complete application.

First, we need to refactor the previous code slightly so that it can return the correct information.

File: /home/matt/Github/ice_breaker/ice_breaker.py
```python
from langchain_core.prompts import PromptTemplate
from langchain_openai import ChatOpenAI

from third_parties.linkedin import scrape_linkedin_profile
from third_parties.twitter import scrape_user_tweets
from agents.linkedin_lookup_agent import lookup as linkedin_lookup
from agents.twitter_lookup_agent import lookup as twitter_lookup
from output_parsers import Summary, summary_parser
#                          ^^^^^^^
from typing import Tuple, Any # import some types


def ice_break_with(name: str) -> Tuple[Summary, Any]:
#                             ^^^^^^^^^^^^^^^^^^^^^^ define types
    linkedin_url = linkedin_lookup(name=name)
    linkedin_data = scrape_linkedin_profile(linkedin_profile_url=linkedin_url)

    twitter_username = twitter_lookup(name=name)
    user_tweets = scrape_user_tweets(username=twitter_username)

    summary_template = """
        given the information about a person from linkedin {information},
        and lastest twitter posts {twitter_posts}. I want you to create:
            1. a short summary
            2. two interesting facts about them
        
        Use both the linkedin information and the twitter posts to create the summary and the interesting facts.\n{format_instructions}"""

    summary_prompt_template = PromptTemplate(
        input_variables=['information', 'twitter_posts'],
        template=summary_template,
        partial_variables={"format_instructions": summary_parser.get_format_instructions()}
    )

    llm = ChatOpenAI(temperature=0, model="gpt-4o-mini")

    chain = summary_prompt_template | llm | summary_parser

    res: Summary = chain.invoke(input={
        "information": linkedin_data,
        "twitter_posts": user_tweets
    })

    return res, linkedin_data.get("profile_picture_url")
#   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ replace print with return, so the frontend can use the data
#                                                        besides summary data, we return linkedin profile picture url as well


if __name__ == '__main__':
    ice_break_with("Eden Marco")


```


Then, we create a flask application to serve the frontend and the backend.


File: /home/matt/Github/ice_breaker/app.py (new file)
```python
from flask import Flask, render_template, request, jsonify
from ice_breaker import ice_break_with


app = Flask(__name__)

@app.route('/')
def index():
    return render_template('index.html')
    #                      ^^^^^^^^^^^^ for the frontend rendering, we'll copy a boilerplate html file to /templates/index.html

# when user input a name and submit the form, the frontend will send a POST request to /process
@app.route('/process', methods=['POST'])
def process():
    name = request.form["name"] # get the name from the form that user input
    summary, profile_pic_url = ice_break_with(name=name) # call the function we defined in ice_breaker.py, it'll return a summary and a profile picture url
    # convert the result to a json format, so it can be sent back to the frontend
    return jsonify({
        "summary_and_facts": summary.to_dict(),
        #                           ^^^^^^^^^^ Summary object cannot be directly converted to json, so we need to convert it to a dictionary first
        "picture_url": profile_pic_url
    })


if __name__ == '__main__':
    app.run(debug=True)


```

Now, we can run the flask application and see the result in the browser.
When user input a name and submit the form, wait for a few seconds, the application will show the summary, interesting facts and the avatar of the person.


Below is the boilerplate html file that we'll use for the frontend. We'll copy it to /templates/index.html
We just need to make sure the form in the html file sends a POST request to /process, and the javascript code will handle the response and update the page with the result.

File: /home/matt/Github/ice_breaker/templates/index.html
```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Ice Breaker</title>
        <link rel="stylesheet" href="https://unpkg.com/mvp.css">
        <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@chgibb/css-spinners@2.2.1/css/spinner/three-quarters.min.css">
    </head>
    <body>
        <header>
            <form id="name-form" style="margin: 0 auto">
                <h1>Ice Breaker</h1>
                <input type="text" name="name" placeholder="Enter name">
                <button id="magic-button" type="submit" class="center">Do Your Magic</button>
            </form>
        </header>
        <div id="spinner" style="text-align: center; display: none">
            <span class="three-quarters-loader" style="width: 100px; height: 100px; border-radius: 50%; border-width: 12px;"></span>
        </div>
        <main id="result" style="display: none">
            <div style="text-align: center">
                <img id="profile-pic" src="" alt="Profile Picture" style="width: 300px; max-width: 100%; height: auto; border-radius: 50%; margin-bottom: 20px;">
            </div>
            <div>
                <h2>Summary</h2>
                <p id="summary"></p>
            </div>
            <div>
                <h2>Interesting Facts</h2>
                <div id="facts"></div>
            </div>
            <div>
                <h2>Ice Breakers</h2>
                <div id="ice-breakers"></div>
            </div>
            <div>
                <h2>Topics of Interest</h2>
                <div id="topics-of-interest"></div>
            </div>
        </main>

        <script>
            const form = document.getElementById("name-form");
            const spinner = document.getElementById("spinner");
            const result = document.getElementById("result");

            form.addEventListener("submit", (ev) => {
                ev.preventDefault();

                result.style.display = "none";
                spinner.style.display = "";

                const formData = new FormData(form);

                //Here is the point where we send a POST request to the backend
                fetch("/process", {method: "POST", body: formData})
                    .then(response => {
                        if (response.ok) return response.json(); // if the response is ok, return the json data
                        throw new Error("POST request failed");
                    })
                    // using the json data to update the page
                    .then(data => {
                        document.getElementById("profile-pic").src = data.picture_url;
                        document.getElementById("summary").textContent = data.summary_and_facts.summary;
                        createHtmlList(document.getElementById("facts"), data.summary_and_facts.facts);
                        createHtmlList(document.getElementById("ice-breakers"), data.ice_breakers.ice_breakers);
                        createHtmlList(document.getElementById("topics-of-interest"), data.interests.topics_of_interest);

                        spinner.style.display = "none";
                        result.style.display = "";
                    })
            });

            function createHtmlList(element, items)
            {
                const ul = document.createElement("ul");

                items.forEach(item => {
                    const li = document.createElement("li");
                    li.textContent = item;
                    ul.appendChild(li);
                })

                element.innerHTML = "";
                element.appendChild(ul);
            }
        </script>
    </body>
</html>
```

