Description

Ce projet est une API GraphQL de gestion de tâches développée avec Node.js, Express, et Apollo Server. Il permet d'ajouter, modifier, supprimer et récupérer des tâches via des requêtes GraphQL.

🚀 Installation et Configuration

1️⃣ Cloner le dépôt

git clone https://github.com/VOTRE-UTILISATEUR/NOM-DU-REPO.git
cd NOM-DU-REPO

2️⃣ Installer les dépendances

npm install

3️⃣ Lancer le serveur

node index.js

Ensuite, ouvrez votre navigateur et accédez à :

http://localhost:5000/graphql

📂 Structure du Projet

tp-graphql/
│── index.js         # Configuration du serveur Apollo et Express
│── taskSchema.gql   # Définition du schéma GraphQL
│── taskSchema.js    # Construction du schéma GraphQL
│── taskResolver.js  # Résolveurs pour gérer les requêtes et mutations
└── package.json     # Dépendances du projet

🏗️ Implémentation

1️⃣ Schéma GraphQL (taskSchema.gql)

type Task {
  id: ID!
  title: String!
  description: String!
  completed: Boolean!
  duration: Int!
}

type Query {
  task(id: ID!): Task
  tasks: [Task]
}

type Mutation {
  addTask(title: String!, description: String!, completed: Boolean!, duration: Int!): Task
  completeTask(id: ID!): Task
  changeDescription(id: ID!, description: String!): Task
  deleteTask(id: ID!): Boolean
}

2️⃣ Configuration du Serveur (index.js)

const express = require('express');
const { ApolloServer } = require('@apollo/server');
const { expressMiddleware } = require('@apollo/server/express4');
const { json } = require('body-parser');
const { addResolversToSchema } = require('@graphql-tools/schema');
const taskSchemaPromise = require('./taskSchema');
const taskResolver = require('./taskResolver');

const app = express();

async function setupServer() {
  try {
    const taskSchema = await taskSchemaPromise;
    const schemaWithResolvers = addResolversToSchema({ schema: taskSchema, resolvers: taskResolver });
    const server = new ApolloServer({ schema: schemaWithResolvers });
    await server.start();
    app.use('/graphql', json(), expressMiddleware(server));
    const PORT = process.env.PORT || 5000;
    app.listen(PORT, () => console.log(`🚀 Serveur lancé sur http://localhost:${PORT}/graphql`));
  } catch (error) {
    console.error('Erreur serveur Apollo:', error);
  }
}
setupServer();

🔥 Exemples de Requêtes GraphQL

📌 Récupérer toutes les tâches

query {
  tasks {
    id
    title
    description
    completed
    duration
  }
}

➕ Ajouter une nouvelle tâche

mutation {
  addTask(title: "Nouvelle Tâche", description: "Description", completed: false, duration: 60) {
    id
    title
  }
}

✏️ Modifier la description d’une tâche

mutation {
  changeDescription(id: "1", description: "Nouvelle description") {
    id
    description
  }
}

🗑️ Supprimer une tâche
