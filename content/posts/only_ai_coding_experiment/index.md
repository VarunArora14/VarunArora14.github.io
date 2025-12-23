---
title: "Only Vibe Coding Experiment"
date: 2025-12-10T14:43:12+05:30
draft: true
tags: ["LLM", "Vibe Coding"]
---

### Let's Vibe Code

My team encountered an interesting directive from leadership: to **exclusively use "vibe coding"** (AI-driven development) to build applications. The goal was to accelerate time to market and streamline feature delivery.

The existing setup relied on numerous Java Spring Boot microservices, which were perceived as heavy and slower to iterate on compared to a JavaScript/TypeScript ecosystem. The hypothesis was that since LLMs excel at TypeScript, switching tech stacks would allow us to leverage AI more effectively than with enterprise Java.

So now a POC began with the following steps - 

1. take an existing small serverless application
2. create a prompt to get it's functionality from LLM
3. pass the prompt again to LLM in Agent mode in GitHub copilot with empty folder data so it generates (hopefully) same code again
4. review the new code and share feedback with leadership

It was an interesting experiment until we realized the constraint: we had to replicate the existing application's behavior exactly, but were restricted to modifying a *single* prompt. We were attempting a 'one-shot' generation for an entire application.

If errors occurred, the process required starting a new LLM session, fixing the error contextually, and re-prompting. It took days to approximate the existing functionality via prompt changes and manual reviews. We eventually hit a plateau where the LLM refused to apply certain changes despite explicit instructions. The prompt grew to over 1,000 lines. We eventually had to intervene manually to fix logic errors and unblock the POC.

It became clear that while LLMs can produce impressive results, strict "one-shot" constraints introduce a significant time penalty compared to iterative coding.

We provided feedback that writing a single shot prompt to create an entire application was inefficient and resulted in logic errors that were hard to debug. A more structured approach was needed—input/output schemas, business logic, and workflows had to be defined during a planning phase, not as an afterthought.

After multiple discussions, we pivoted to a "blueprint" and "plan" approach for the next application, which involved converting a service from Java to NodeJS.

### BluePrint Prompts

This new method of dividing a big prompt to smaller sub-prompts which further worked as blueprints in a workflow like manner seemed to be more systematic and methodological where we spent more time on planning what has to be done and created a smaller prompt for each problem. For our next application, we had multiple blueprint prompts made with tens of hours of efforts, containing instructions of feature stories to create, features to build, unit tests to create and review prompts as well.

Zooming on the feature building prompt, here is further breakdown of the steps we followed - 

- Prompt the LLM to extract the functionality out of the java application
  -  do this multiple times if some functionalities are missing
  -  do proper review manually reading thousands of lines of stories/functional and Non-functional requirements created
- Pass this prompt as context with new prompt to create this new nodeJS application
- Manually review the generated files to determine if the changes are acceptable.
  - If unacceptable, the cycle repeats: rewrite the prompt, wait for generation, and review thousands of lines of code again.
  - If acceptable, you proceed, often trusting the AI's output significantly.
- Run the application and manually verify all the functionalities working with Postman collection created

After this, unit tests prompt was create and run, which created unit tests and then these tests should be all passing 100% as well before creating PR.

This process for rewriting a 5-10k line codebase took significant time in prompt review and iteration. Even then, the application rarely started without configuration or logic errors on the first try.

We got tons of errors due to various reasons - dependency versions, wrong/missing configs, incorrect server start file, incomplete API endpoints etc

And these are errors that are stopping the application to start. After that comes - 
- **Anti-patterns**: LLMs frequently wrote anti-patterns or ignored project-specific conventions.
- **Security & Config**: CORS and SSL errors persisted despite explicit handling instructions in the prompt.
- **Logic Errors**: Subtle business logic bugs were persistent and hard to catch in the generated code.
- **Framework Mismatch**: The functionality often deviated from the Java reference because concepts like Dependency Injection or JPA don't map 1:1 to plain TypeScript without a robust framework, which the LLM struggled to scaffold correctly.

***

By the end of this procedure, the team was fatigued. We were often pasting commandline errors directly into the AI's **Agent Mode**, hoping it would resolve them. Frequently, it would fix three bugs only to introduce four new ones. A common trend emerged: without a deep understanding of the underlying system, we felt we were moving forward when we were actually circling the same problems.

Worst of all, after months of this workflow, I felt my core coding skills were on a decline. I found myself forgetting basic syntax because I was so reliant on the model to handle the implementation details.

***

### The Trap of Over Reliance

When manual review became too time consuming, the temptation to blindly copy paste errors into the chat window became irresistible. Whether the model fixed the logic, refactored the code, or modified the tests to force a pass, we devs often accepted them without much scrutiny.

This led to a pattern of shortcuts across the team:

- **Architecture**: Critical design decisions were sometimes offloaded to the LLM without sufficient constraint or review.
- **Requirements**: User stories were generated via prompts, missing edge cases and specific business contexts.
- **Implementation**: Developers acted as middlemen between the compiler giving errors and the LLM, without understanding the 'why' behind a fix.
- **Testing**: Test cases were often AI generated, which created a risk of circular validation where the AI writes tests that pass its own buggy code.

With logical thinking partially offloaded to LLMs, confusion grew regarding the application's actual behavior versus its desired functionality.

After 2 weeks of heavy vibe coding and prompting to make a 8k+ lines PR which I had to explain to Architect what each method and code block does, it got 100+ review comments that I fixed with teammate in next 2 days and merged into main.

The best part of this whole process of us understanding the code line by line as we had to explain it in same way to our Architect and they gave nitpicks as well which made this code more aligned to desired structure. Thankfully, since we had so much discussion regarding each method and logic, our team's tests hardly failed and did not require any further changes while other team doing similar work had many QA tests failing and required logical rework for few components due to bad vibes.

As pressure built on us to ship fast, everyone made a choice to take the **easy** path rather than the **simple** one. The easy path was to choose **speed now and complexity later**, whereas the **simple** path required sitting down to make correct architectural choices. The simple path was avoided because it felt "slower," and in a race where every company claims to ship fast with AI, we felt the need to ship even faster.

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
- **Managing Expectations** - There was a disconnect regarding *how* AI automates work. When blockers arose, the suggestion was often to "use the AI more," assuming it was a magic wand. However, when AI-generated code failed, the validity of the tool itself was questioned. It created a tension between "move fast with AI" and "why is the AI breaking things?"

***

I do believe that tools like Cursor and GitHub Copilot have made development way faster than before and are starting to feel as **needed tooling** nowadays! But see, the issue was never that devs aren't writing enough lines of code. There was never a competition that the best product has the most lines of code and so use LLMs to write them fast. It was always about time taken to understand the architecture, design choices, understanding the code flow, team collaboration and information sync, breaking up new features into small tasks to divide among devs and build. Till now Arcitects and Senior engineers chose to slow down development and add blockers (as managers say) to discuss implementation logic on whiteboards and meetings to understand the details of features being built to de-risk the software development avoiding future rework bug fixes. This de-risking the software release avoided ton of pitfalls that slowed the entire team down previosuly.

With LLMs, it has become fairly easier to **understand the design, architecture and code flow** and even more easier to create POCs to fail early when coming up with ideas to build but the time and effort required for human devs to **choose the architecture and design of their system with their tradeoffs** based on their customers cannot be replace by these LLMs. Nor can it replace experience devs who will look at new code suggestions or PRs and without running code and checking each functionality from UI, can find anti patterns and logical errors sheerly due to their understanding or should I call it the **context** they have of an application which cannot be replicated by LLM prompts with memories.

To me these tools seem promising to suggest code and improve understanding of existing systems, making it easier for people to gain knowledge of anything with just natural language instructions, but the idea of agents replacing humans seems far from reality to me.

We handcrafted elaborate prompts for **story creation**, **code structure**, **requirements**, and **testing**. Yet, these workflows often faltered because they still required human developers to deeply review the output. If the "vibe coded" agentic development works on the first try, it's a massive win. If it fails, you've spent significant tokens and human hours on a review process that is often harder than writing the code from scratch.

Q. Is this effort and money acceptable for a POC or a startup application to reach market early? 

A. **Probably Yes!** The loss would be only some million tokens and some hours and upside could be your application valued in millions! It seems like a risk worth taking to many, especially on small scale.

Q. Is this effort and money acceptable for a large scale application that might require total billions of tokens to add features and thousands of development hours which will have many bugs and might turn into technical debt fast?

A. **Probably Not!** If the existing application as the end result becomes more unstable with addition of bugs and security issues (common via vibe coding) and has AI generated QA tests as well then while it might seem development is going on well with testing, but it is actually making the software more flawed than before. It becomes important to de-risk the application release, especially on large scale where even small security bugs can lead to massive problems for companies and their users. 

***

At the end, impact matters the most. If you build a complex application via vibe coding in a short amount of time then it's a great promise despite the bugs and software quality issues it has, it can now be easily replaced by another application built via another dev using same vibe coding you did. 

**So where is your leverage apart from faster delivery to market which now everyone has? If everyone has the same leverage then probably it's not a leverage anymore.** 

To me, it feels that stability, reliability, and security trust—along with ease of usage—will become the true differentiators for software experiences as we move further into 2026. 

I would be using GitHub copilot as a part of my daily driver with Gemini or ChatGPT web in future as well to learn and suggest code to improve and speed up my learning. At the same time I believe my knowledge and understanding of systems and how to solve problems will remain my biggest leverage as I refuse to offload my problem solving and thinking capabilities to LLMs.
