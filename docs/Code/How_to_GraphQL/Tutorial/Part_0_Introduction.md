## Introduction

In this section, you will set up all of the pieces needed to build a GraphQL API. You will start up a TypeScript project, provision a PostgreSQL database, initialize Prisma in your project, and finally seed your database.

In the process, you will set up an important piece of the end-to-end type-safety puzzle: a source of truth for the shape of your data.

### Technologies you will use

These are the main tools you will be using throughout this series:

- [Prisma](https://www.prisma.io/) as the Object-Relational Mapper (ORM)
- [PostgreSQL](https://www.postgresql.org/) as the database
- [Railway](https://www.sqlite.org/index.html) to host your database
- [TypeScript](https://www.typescriptlang.org/) as the programming language
- [GraphQL Yoga](https://www.graphql-yoga.com/) as the GraphQL server
- [Pothos](https://pothos-graphql.dev/) as the code-first GraphQL schema builder
- [Vite](https://vitejs.dev/) to manage and scaffold your frontend project
- [React](https://reactjs.org/) as the frontend JavaScript library
- [GraphQL Codegen](https://www.graphql-code-generator.com/) to generate types for the frontend based on the GraphQL schema
- [TailwindCSS](https://tailwindcss.com/) for styling the application
- [Render](https://render.com/) to deploy your API and React Application

### Assumed knowledge

While this series will attempt to cover everything in detail from a beginner's standpoint, the following would be helpful:

- Basic knowledge of JavaScript or TypeScript
- Basic knowledge of GraphQL
- Basic knowledge of React

### Development environment

To follow along with the examples provided, you will be expected to have:

- [Node.js](https://nodejs.org/) installed.
- The [Prisma VSCode Extension](https://marketplace.visualstudio.com/items?itemName=Prisma.prisma) installed. _(optional)_
