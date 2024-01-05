# Get started

Hello there! This is your first step to becoming an expert at taking
R applications to production. In this section you will find many
tutorial articles that will help you get up and running with the
prodverse.

[Shiny in production with the prodverse](shiny_in_prod.md) is a
tutorial that will take you through the process of building a very
minimal Shiny application with user authentication and deploying it to
a server with replicas and load balancing using **faucet** and
**tapLock**. (The app in the tutorial is very minimal and not
particularly useful, but it is a good starting point for understanding
the prodverse.)

[Scaling Plumber with faucet](scaling_plumber.md) is a tutorial
that will walk you through the process of scaling a Plumber API with
**faucet**. First you will install faucet and test it out locally,
then you will use Docker to build a container image for your API.
The API will be slow to show how **faucet** will attempt to make it
faster by adding replicas and load balancing.

