# microservice-with-node
Learn Microservice With NodeJs

## Prerequisites for building microservices architecture with Node.js
o Node.js and npm (The Node.js installation will also install npm.)

# Start
## Create the heroes service
Go to the directory under which you’d like to create the project and create following directory and file structure:

``` 
./heroes/heroes.js
./heroes/img/
```
If you’d like to use source code control, this would be a good time to initialize a repository. Don’t forget to add a .gitignore file like this one if you’re using Git.

Initialize the npm project inside ./heroes directory and install necessary dependencies by executing the following command instructions:

```
npm init -y
npm install express body-parser
```
It’s time to implement the service. Copy this JavaScript code to the ./heroes/heroes.js file:

```
const express = require('express');
const path = require('path');
const bodyParser = require('body-parser');

const port = process.argv.slice(2)[0];
const app = express();
app.use(bodyParser.json());

const powers = [
  { id: 1, name: 'flying' },
  { id: 2, name: 'teleporting' },
  { id: 3, name: 'super strength' },
  { id: 4, name: 'clairvoyance'},
  { id: 5, name: 'mind reading' }
];

const heroes = [
  {
      id: 1,
      type: 'spider-dog',
      displayName: 'Axe',
      powers: [1, 4],
      img: 'axe.jpg',
      busy: false
  },
  {
      id: 2,
      type: 'flying-dogs',
      displayName: 'Blodseeker',
      powers: [2, 5],
      img: 'blodseeker.jpg',
      busy: false
  },
  {
      id: 3,
      type: 'dark-light-side',
      displayName: 'Lion',
      powers: [3, 2],
      img: 'lion.jpg',
      busy: false
  },
  {
      id: 4,
      type: 'captain-dog',
      displayName: 'Mirana',
      powers: [1, 5],
      img: 'mirana.jpg',
      busy: false
  }
];

app.get('/heroes', (req, res) => {
  console.log('Returning heroes list');
  res.send(heroes);
});

app.get('/powers', (req, res) => {
  console.log('Returning powers list');
  res.send(powers);
});

app.post('/hero/**', (req, res) => {
  const heroId = parseInt(req.params[0]);
  const foundHero = heroes.find(subject => subject.id === heroId);

  if (foundHero) {
      for (let attribute in foundHero) {
          if (req.body[attribute]) {
              foundHero[attribute] = req.body[attribute];
              console.log(`Set ${attribute} to ${req.body[attribute]} in hero: ${heroId}`);
          }
      }
      res.status(202).header({Location: `http://localhost:${port}/hero/${foundHero.id}`}).send(foundHero);
  } else {
      console.log(`Hero not found.`);
      res.status(404).send();
  }
});

app.use('/img', express.static(path.join(__dirname,'img')));

console.log(`Heroes service listening on port ${port}`);
app.listen(port);
```

Download the superhero and superhero team pictures from the following links and place them in the /heroes/img directory:

```
o axe.jpg
o blodseeker.jpg
o lion.jpg
o mirana.jpg
```

## Test the heroes.js service

Run the service by executing the following command line instruction:
```
node ./heroes/heroes.js 8081
```

You can check to see if the service works as expected by using Postman, curl, PowerShell Invoke-WebRequest, or your browser. The curl command line instruction is:

o Postman or your browser
``` 
localhost:8081/heroes 
```
o Command line
```
curl -i --request GET localhost:8081/heroes
```

In Postman the body of the response should look like this:
```
[
    {
        "id": 1,
        "type": "spider-dog",
        "displayName": "Axe",
        "powers": [
            1,
            4
        ],
        "img": "axe.jpg",
        "busy": false
    },
    {
        "id": 2,
        "type": "flying-dogs",
        "displayName": "Blodseeker",
        "powers": [
            2,
            5
        ],
        "img": "blodseeker.jpg",
        "busy": false
    },
    {
        "id": 3,
        "type": "dark-light-side",
        "displayName": "Lion",
        "powers": [
            3,
            2
        ],
        "img": "lion.jpg",
        "busy": false
    },
    {
        "id": 4,
        "type": "captain-dog",
        "displayName": "Mirana",
        "powers": [
            1,
            5
        ],
        "img": "mirana.jpg",
        "busy": false
    }
]
```

# Next Step
If you want to catch up to this step using the code from the GitHub repository, execute the following commands in the directory where you’d like to create the project directory:
```
git clone https://github.com/imam932/microservice-with-node.git
cd microservice-with-node
git checkout step1
npm install
```

## Create the threats service
What’s the purpose of a superhero if there’s no peril? The microservices architecture of our application uses a separate service to represent the challenges that only a superhero can overcome. It also provides an API endpoint for matching superheros to threats.

The procedure for creating the threats service is similar to heroes service.

In the main directory of your project, create following directory and file structure:
```
./threats/threats.js
./threats/img/
```