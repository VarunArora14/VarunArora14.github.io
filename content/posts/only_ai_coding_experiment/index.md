---
title: "Only Vibe Coding Experiment"
date: 2026-12-10T14:43:12+05:30
draft: true
tags: ["LLM", "Vibe Coding"]
---

### Let's Vibe Code

My team came across an interesting requirement that the senior and principal architects wanted everyone to **only vibe code** from now to build applications faster to market so they can onboard clients faster and can build the features quickly.

The existing setup had numerous Java Spring Boot microservices which felt harder to maintain and run locally as code had to be compiled each time with new changes whereas JavaScript/TypeScript uses JIT compiler that compiles on save and this seemed to be a game changer to them. Plus the fact that LLMs are heavily trained on TypeScript code, it will give suggestions and make code changes much better than it could do to enterprise Java code.

So now a POC began with the following steps to follow - 

1. take an existing small serverless application
2. create a prompt to get it's functionality from LLM
3. pass the prompt again to LLM in Agent mode in GitHub copilot with empty folder data so it generates (hopefully) same code again
4. review the new code and share feedback with leadership

It seemed like an interesting experiment and we did it, until we realised we had to make this app working exactly as the previous application and then share insights. To make this even more interesting, we were told to ONLY change the single prompt used (no additional prompts allowed) and make the application working in single shot!

If it gives errors, then open a new LLM session, fix the error and then pass the fix in the prompt again to run the whole process again! I could not imagine I had to follow this, but we had to. It took 3 days to come close to the existing application with multiple people trying multiple prompts to see which one gives them best results. We eventually hit a plateau where we found the LLM was just not doing a set of changes despite them being in the prompt. The prompt was now more than 1000+ lines (and had emojis 😊) and now I had to manually make the code changes by simply copying some content for the previous application and manually fixing logic error for the POC to end.

It was becoming clear to me that LLM with proper prompts can give results close to desired results but it comes at a BIG time cost which needs to be further optimised for development to actually be faster.

We sent the feedback that writing a single shot prompt to create an entire application did not make sense and it required much more manual changes. There were also many logic errors which have to be resolved and understood in planning phase and should not be an afterthought while fixing errors. The input and output schema, business logic, workflows have be to created to not go in circles later on.

After multiple discussions and going through the way other people are doing vibe coding with **blueprint** and **plan** prompts, we were told to come up with same for our next application to convert from Java to NodeJS.

### BluePrint Prompts

This new method of dividing a big prompt to smaller sub-prompts which further worked as blueprints in a workflow like manner seemed to be more systematic and methodological where we spent more time on planning what has to be done and created a smaller prompt for each problem. For our next application, we had multiple blueprint prompts made with tens of hours of efforts, containing instructions of feature stories to create, features to build, unit tests to create and review prompts as well.

Zooming on the feature building prompt, here is further breakdown of the steps we followed - 

- Prompt the LLM to extract the functionality out of the java application
  -  do this multiple times if some functionalities are missing
  -  do proper review manually reading thousands of lines of stories/functional and Non-functional requirements created
- Pass this prompt as context with new prompt to create this new nodeJS application
- Now go through the files created manually reading through the functionality created and try to determine whether these changes are acceptable or not
  - If they are not acceptable, get ready to hate yourself even more as you write the prompt again, wait for 10-15 minutes and have to read through thousands of lines again, just to decide whether these changes are acceptable or not!
  - If you are accepting then most probably you have either given up or trust the AI to be way smarter than you
- Run the application and manually verify all the functionalities working with Postman collection created

After this, unit tests prompt was create and run, which created unit tests and then these tests should be all passing 100% as well before creating PR.

This whole processing of rewriting this new application for a 5-10k size codebase could take around a day with reviews of prompts and you would be hoping the app starts and endpoints are all working. They are not.

We got tons of errors due to various reasons - dependency versions, wrong/missing configs, incorrect server start file, incomplete API endpoints etc

And these are errors that are stopping the application to start. After that comes - 
- LLM writing anti-pattern code throughout the repository, not following the rules
- CORS and SSL errors are very often despite putting the correct way to handle them in prompt
- Logic errors in business logic is always there no matter what
- The new created functionalities heavily deviates from existing Java application as Java + Spring boot code cannot be directly put in plain TypeScript. Dependency Injection, Inversion of Control, JPA/Hibernate, other plugins are not directly available as solutions in TS/JS environments to be used.

***

After going through this whole procedure, we were starting to hate ourselves as we understood nothing but are just taking errors from commandline and pasting into copilot **Agent Mode** in hopium that it resolves them. And to our suprise, it solves 3 bugs and created 4 more. This has been a very common trend in my experience that unless someone with deep understanding and knowledge of end result is using these models, they end up working hours thinking they are moving forward, but are rather going in circles around the same problem.

And the worst part is, after 3 months of vibe coding, I felt I lost more knowledge than I could learn during this team and felt dreaded due to cut throat deadlines to build it. I forgot the JavaScript syntax to make **fetch** requests while writing domain layer, business layer and repository layer code for a big backend application.

***

### Taking Shortcuts For Fixing Errors

When it was found to be too time consuming to read and understand everything, our reaction was to simply copy the errors and paste into chat window for agentic model to fix it. Whether it fixes the logic only or changes code structure or modifies the tests for 100% coverage, it's the model's choice now.

Despite not writing manual code, it felt dreaded and mentally tired after performing these tasks. It seemed like everyone was just taking shortcuts via LLM to get things done - 

- Architect was telling LLM to create architecture and core architecture prompts without much review
- Product Manager used prompts to create stories without proper validation and scope check
- Devs ran the prompts and copied the errors into LLM to fix it and understood code partially with unit tests created with prompts as well
- Testing team used prompts to create their integration and E2E tests 

Since everyone was offloading their logical thinking to LLMs, we had ton of confusion regarding the desired application and functionality we wanted and combine this with hard deadlines to wrap things fast, it was a big mess for everyone.

After 2 weeks of heavy vibe coding and prompting to make a 8k+ lines PR which I had to explain to Architect what each method and code block does, it got 100+ review comments that I fixed with teammate in next 2 days and merged into main.

The best part of this whole process of us understanding the code line by line as we had to explain it in same way to our Architect and they gave nitpicks as well which made this code more aligned to desired structure. Thankfully, since we had so much discussion regarding each method and logic, our team's tests hardly failed and did not require any further changes while other team doing similar work had many QA tests failing and required logical rework for few components due to bad vibes.

As more pressure was built on use to ship fast, everyone made a choice to take an **easy** path and not the **simple** one. The easy path was to choose **speed now and complexity later** whereas the **simple** path was to sit and think about making correct choices. The **simple** path was now being avoided as it was **slower** and if every company is saying they are shipping fast with AI, we needed to ship even faster!

This whole 3 month stint was quite a lesson for me for how to use and not use LLMs to utilise them for my workflows while developing applications.

***

## My Vibe Coding TakeAways 

### What Worked Well

- Getting additional information as suggestions related to code review provided a larger perspective to understand where code could fail and what best practices we can use further
- LLMs are fairly good at standard architecture design and code structure so using to learn HLD and suggest architecture for building applications can help a lot (provided you know HLD before hand while designing for enterprise)
- Using Agent mode in Github Copilot worked well for smaller changes which are scoped and can be easily reviewed. This required scope to be created as prerequisite.
- Using Ask mode to suggest and review code feels much safer and controlled. It helped me keep my confidence and understanding way better than letting Agent mode run various commands and multi file changes which are very hard to track
- Using LLMs for **code analysis and documentation** boosted our productivity. Understanding existing code became way way faster as LLMs helped provide holistic view of applications and code workflows despite old codebase not having documentation. Asking it for architecture and flow of the repository you are working with gave great insights which would have taken much longer if done without it's help.
- LLMs are pretty good at creating documentations as well for existing and new code base but requires proper review. It is way faster and easier to use LLMs for documentation and understanding code as personal experience

### What Didn't Work Well

- **Prompt engineering did not get the work done** - It required way more prompts and then manual code changes to get desired functionalities even with great prompts
- **Don't waste time creating curated and complete prompts**  After sometime either the prompt will be very large or it will take so much time to create prompts that the development speed will feel slower than before. The way I do is to solve big problems/tasks into smaller problems for building stuff and put in markdown format (think like Jira story) and then for each smallest subtask, I give smaller prompt of max 3-10 lines with task details mentioned to perform. This leads to smaller changes to review and validate
- **Don't offload the logic to LLMs** - Having deep understanding of existing and desired business logic is important as otherwise you might feel lost due to scope creep and anti patterns created throughout codebase by LLMs which would feel hard to understand and then you might give up thinking
- **Lack of Planning** - Before writing single line code, had we planned the desired stage to reach, we would have avoided a lot of time wasted in creating and cleaning messy code. The quote "Give me six hours to chop down a tree and I'll spend the first four sharpening the axe" makes a lot more sense to me now as I wish to first fully understand the things to be done before writing a single line of code
- **Management expectations** - There was a growing disconnect. When we hit blockers, the solution was always "use the AI more." I heard classic lines like, "Why is it taking so long if we have AI?" or "Don't just ask why, find the solution." But when the AI-generated code failed, the tone shifted to "You can't use AI blindly." It became a catch 22 situation for us devs.

***

I do believe that tools like Cursor and GitHub Copilot have made development way faster than before and are starting to feel as **needed tooling** nowadays! But see, the issue was never that devs aren't writing enough lines of code. There was never a competition that the best product has the most lines of code and so use LLMs to write them fast. It was always about time taken to understand the architecture, design choices, understanding the code flow, team collaboration and information sync, breaking up new features into small tasks to divide among devs and build. Till now Arcitects and Senior engineers chose to slow down development and add blockers (as managers say) to discuss implementation logic on whiteboards and meetings to understand the details of features being built to de-risk the software development avoiding future rework bug fixes. This de-risking the software release avoided ton of pitfalls that slowed the entire team down previosuly.

With LLMs, it has become fairly easier to **understand the design, architecture and code flow** and even more easier to create POCs to fail early when coming up with ideas to build but the time and effort required for human devs to **choose the architecture and design of their system with their tradeoffs** based on their customers cannot be replace by these LLMs. Nor can it replace experience devs who will look at new code suggestions or PRs and without running code and checking each functionality from UI, can find anti patterns and logical errors sheerly due to their understanding or should I call it the **context** they have of an application which cannot be replicated by LLM prompts with memories.

To me these tools seem promising to suggest code and improve understanding of existing systems, making it easier for people to gain knowledge of anything with just natural language instructions, but the idea of agents replacing humans seems far from reality to me.

We ourselves had this setup where we hand(vibe) crafted prompts for **story creation**, **code structure**, **functional requirements**, **code review**, **unit testing**, **QA testing** and yet these workflows failed for us. It was because it required human developers to further review this all mess created as whether these actually work as expected or not. If this vibe coded agentic development works in first or second shot then yes you won! If it fails, then you wasted millions of tokens (real money btw), hours of time and human effort to review for nothing.

Q. Is this effort and money acceptable for a POC or a startup application to reach market early? 

A. **Probably Yes!** The loss would be only some million tokens and some hours and upside could be your application valued in millions! It seems like a risk worth taking to many, especially on small scale.

Q. Is this effort and money acceptable for a large scale application that might require total billions of tokens to add features and thousands of development hours which will have many bugs and might turn into technical debt fast?

A. **Probably Not!** If the existing application as the end result becomes more unstable with addition of bugs and security issues (common via vibe coding) and has AI generated QA tests as well then while it might seem development is going on well with testing, but it is actually making the software more flawed than before. It becomes important to de-risk the application release, especially on large scale where even small security bugs can lead to massive problems for companies and their users. 

***

At the end, impact matters the most. If you build a complex application via vibe coding in a short amount of time then it's a great promise despite the bugs and software quality issues it has, it can now be easily replaced by another application built via another dev using same vibe coding you did. 

**So where is your leverage apart from faster delivery to market which now everyone has? If everyone has the same leverage then probably it's not a leverage anymore.** 

To me it feels like that now stability, reliability and trust for security along with ease of usage might turn out to be of higher importance for software experiences as we move further into 2026. 

I would be using GitHub copilot as a part of my daily driver with Gemini or ChatGPT web in future as well to learn and suggest code to improve and speed up my learning. At the same time I believe my knowledge and understanding of systems and how to solve problems will remain my biggest leverage as I refuse to offload my problem solving and thinking capabilities to LLMs.
