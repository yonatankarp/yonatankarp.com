---
title: "Enhance Productivity with a Repository Template"
subtitle: "From Repetitive Boilerplate to Rapid Prototyping in a Snap!"
slug: enhance-productivity-with-a-repository-template
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, github, project-management, ci-cd, software-development-tools
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/M2Kxb80gqcc/upload/35dc7cae9ebbacd3bcd2c3047cc867a8.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Revolutionize Your Workflow with GitHub Template Repositories"
seoDescription: "Transform Your Workflow with GitHub Templates: Streamline project management, automate CI/CD, and boost productivity with versatile repositories"
seriesSlug:
# Table of contents
enableToc: true
saveAsDraft: false
---


A long time ago, a colleague of mine interviewed someone for a position at our company. He was impressed by the person's setup, which allowed them to quickly create projects. This setup included a database connection, REST controllers, and a fast starting process.

Inspired by this story, I realized that I could improve my own workflow. I often found myself skipping projects because of the repetitive boilerplate code I had to add each time.

After some thinking and research, I discovered Template Repositories on GitHub. The concept is simple: create a repository that serves as a foundation for other repositories.

In my case, I identified the following key features that were important to me:

* An easy-to-use script to customize each new clone of the project.
    
* A web framework skeleton with REST controllers.
    
* Dockerizing the application and providing a docker-compose setup for dependencies.
    
* A comprehensive `README` file with information on the purpose of the service and how to run it.
    
* Code quality assurance and security scans for the codebase.
    
* Automatic dependency upgrades to ensure projects are up-to-date.
    
* Database setup.
    
* Test containers for testing the integration between my service and its database.
    

With these requirements in mind, I embarked on my journey.

![golden hour photography of racing bicycle in middle of road](https://images.unsplash.com/photo-1526359395878-56f9a884ea5a?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1000&q=80 align="left")

To centralize my work, I decided to manage everything in a single place: my GitHub repository. I created issues in the repository to track each requirement, ensuring that no good ideas were forgotten over time.

After careful consideration, I settled on the following implementation:

* A [multi-module Gradle project](https://docs.gradle.org/current/userguide/multi_project_builds.html) that allows multiple apps in one repository if needed.
    
* [Spring Boot](https://spring.io/projects/spring-boot) as the web framework, chosen for its popularity.
    
* [SonarCloud](https://www.sonarsource.com/products/sonarcloud/) and [CodeQL](https://codeql.github.com/) for security scans and static code analysis (both free for open-source projects).
    
* [GitHub Actions](https://github.com/features/actions) for CI tooling.
    
* [Dependabot](https://github.blog/2020-06-01-keep-all-your-packages-up-to-date-with-dependabot/) for dependency upgrades, with the added benefit of auto-merging Dependabot's pull requests after passing CI.
    
* [PostgreSQL](https://www.postgresql.org/) with [Flyway](https://flywaydb.org/) as the migration tool and [JOOQ](https://www.jooq.org/) as the query library.
    
* [Spotless](https://github.com/diffplug/spotless) for code-style linting. I was delighted with the results.
    

I was delighted with the results.

![man sitting on mountain cliff facing white clouds rising one hand at golden hour](https://images.unsplash.com/photo-1519834785169-98be25ec3f84?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1000&q=80 align="left")

A few months passed, and I used my template for several small projects. However, one day at work, I noticed that we were falling into the same repetitive patterns I had resolved for myself. Every new project meant creating a new repository, setting up a new deployment, and spending days on work that had minimal impact on our users.

I discussed this with my manager and took it upon myself to fork my template into the organization. Forking alone wasn't sufficient; I needed to tailor it to the organization's specific setup and requirements.

It took a few days of effort, but in the end, I had a fully functional template repository. It enabled me to deploy a new service to production within 1-2 hours of work.

That's what I call a significant improvement!

![closeup photography of gray audio mixer](https://images.unsplash.com/photo-1520950335585-0f7c2f58d481?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1000&q=80 align="left")

However, our story doesn't end there. When generative AIs started becoming prevalent in our lives, I had an idea for a game. The game involved guessing the prompt that was used to generate an AI-generated image.

The concept sounded fun, but I wanted to validate it quickly. I didn't want to spend weeks building something that might not be enjoyable as I anticipated.

Using my template, I managed to create a complete project in just one weekend. I even deployed it to a production environment, allowing me to share it with friends and colleagues to confirm my assumptions about the project's appeal.

P.S. - The game POC is still available at [https://beat-the-machine.yonatankarp.com/](https://beat-the-machine.yonatankarp.com/).

## Conclusion

the final result of my work is available on my GitHub account at [https://github.com/yonatankarp/spring-boot-app-template](https://github.com/yonatankarp/spring-boot-app-template).

---

I hope you enjoyed this journey and learned something new. If you want to stay updated with my latest thoughts and ideas, feel free to register for my [**newsletter**](https://yonatankarp.com/newsletter). You can also find me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!
