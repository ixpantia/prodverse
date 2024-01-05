---
template: overrides/home.html
title: Material for MkDocs
social:
  cards_layout_options:
    title: Documentation that simply works
hide:
  - navigation
  - toc
---

<style>
.container {
    display: flex;
    flex: 1 1 0px;
    width: 100%;
    align-items: center; /* Aligns the image and text vertically */
}

.image {
    width: 50%;
    padding-right: 20px;
    padding-left: 20px;
    /* center the image */
    display: flex;
    justify-content: center;
}

.image img {
    max-height: 400px;
}

.text {
    width: 50%;
    padding-left: 20px;
    padding-right: 20px;
    font-size: 1.5em;
}
</style>

<div class="container">
    <div class="image">
        <img src="/figures/prodverse.png">
    </div>
    <div class="text">
        <h1>prodverse</h1>
        <p>
            The prodverse is a toolbox for taking your data science projects to production.
            The ecosystem is built to help you and your team build, deploy,
            and maintain production-ready data products written with the R programming language.
        </p>
    </div>
</div>

---

## What is the prodverse exactly?

The prodverse is a collection of tools and R packages that help you build, deploy,
and maintain production-ready data products written with the R programming language.

The goals of the prodverse are to:

  - **Make R scalable**: R is a great language for data science, but
    many times the tools to scale it to many users are missing.
    The prodverse aims to fill this gap.
  - **Make R ready for production**: R with Shiny and Plumber are amazing
    tools, but there are many things that are needed to make them production-ready.
    Authentication, logging, and replication are just a few of the things that
    the prodverse provides.
  - **No vendor lock-in**: The prodverse aims to provide a great experience
    anywhere you want to deploy your R applications.

---

## Should I consider the prodverse over other tools?

The prodverse is not for everyone. If you are looking for a tool that allows
for one-click deployment and sharing of your R applications, then you should
consider [Posit Connect](https://posit.co/products/enterprise/connect/). Posit
Connect is a great tool for sharing your R applications with your data science
team or your organization.

If your data science team is not ready for a full-fledged data science platform
like Posit Connect, or needs a more customized solution, then the prodverse
might be a good fit for you.

---

## Need help?

If you need personalized consulting or support for your data science team
please reach out to us at [ixpantia.com](https://www.ixpantia.com/en/contact-us).
