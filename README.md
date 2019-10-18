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
In the ./threats directory, initialize the project and install its dependencies with the following npm command line instructions:
```
npm init -y
npm install express body-parser request
```
Don’t forget to include this project in source code control, if you’re using it.

Place this JavaScript code in the ./threats/threats.js file:
```
const express = require('express');
const path = require('path');
const bodyParser = require('body-parser');
const request = require('request');

const port = process.argv.slice(2)[0];
const app = express();

app.use(bodyParser.json());

const heroesService = 'http://localhost:8081';

const threats = [
  {
      id: 1,
      displayName: 'Pisa tower is about to collapse.',
      necessaryPowers: ['flying'],
      img: 'tower.jpg',
      assignedHero: 0
  },
  {
      id: 2,
      displayName: 'Engineer is going to clean up server-room.',
      necessaryPowers: ['teleporting'],
      img: 'mess.jpg',
      assignedHero: 0
  },
  {
      id: 3,
      displayName: 'John will not understand the joke',
      necessaryPowers: ['clairvoyance'],
      img: 'joke.jpg',
      assignedHero: 0
  }
];

app.get('/threats', (req, res) => {
  console.log('Returning threats list');
  res.send(threats);
});

app.post('/assignment', (req, res) => {
  request.post({
      headers: {'content-type': 'application/json'},
      url: `${heroesService}/hero/${req.body.heroId}`,
      body: `{
          "busy": true
      }`
  }, (err, heroResponse, body) => {
      if (!err) {
          const threatId = parseInt(req.body.threatId);
          const threat = threats.find(subject => subject.id === threatId);
          threat.assignedHero = req.body.heroId;
          res.status(202).send(threat);
      } else {
          res.status(400).send({problem: `Hero Service responded with issue ${err}`});
      }
  });
});

app.use('/img', express.static(path.join(__dirname,'img')));

console.log(`Threats service listening on port ${port}`);
app.listen(port);
```
You can download the pictures from the following links and place in the /threats/img directory:
```
o tower.jpg
o mess.jpg
o joke.jpg
```

Apart from the threats list, and basic methods like listing them, this service also has a POST method, /assignment, which attaches a hero to the given threat:
```
app.post('/assignment', (req, res) => {
   request.post({
       headers: {'content-type': 'application/json'},
       url: `${heroesService}/hero/${req.body.heroId}`,
       body: `{
           "busy": true
       }`
   }, (err, heroResponse, body) => {
       if (!err) {
           const threat = threats.find(subject => subject.id === req.body.threatId);
           threat.assignedHero = req.body.heroId;
           res.status(202).send(threat);
       } else {
           res.status(400).send({problem: `Hero Service responded with issue ${err}`});
       }
   });
});
```

Because the code implements inter-services communication, it needs to know the address of the heroes service, as shown below. If you changed the port on which the heroes service runs you’ll need to edit this line:

```
const heroesService = 'http://localhost:8081';
```
## Test the threats service
If you’ve stopped the heroes service, or closed its terminal window, restart it.

Open another terminal window and start the threats service by executing the following command line instruction:
```
node threats/threats.js 8082
```

In the same way you tested the heroes service, test the threats service by executing a web request using Postman, curl, PowerShell Invoke-WebRequest, or your browser. Note that this time it’s a POST request.

o curl
```
curl -i --request POST --header "Content-Type: application/json" --data '{"heroId": 1, "threatId": 1}' localhost:8082/assignment
```
o Postman
```
method POST
Content-Type: application/json
data : {"heroId": 1, "threatId": 1}
url : localhost:8082/assignment
```

In Postman the body of the response should look like this:
```
{
    "id": 1,
    "displayName": "Pisa tower is about to collapse.",
    "necessaryPowers": [
        "flying"
    ],
    "img": "tower.jpg",
    "assignedHero": 1
}
```
