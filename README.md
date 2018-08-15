# Article API test

#Go version 1.10
#Docker version 18.06.0-ce, build 0ffa825
#docker-compose version 1.21.2, build a133471

## Installing / Getting started

1. For setting up go environment , I pulled this script from github: 
   wget https://raw.githubusercontent.com/canha/golang-tools-install-script/master/goinstall.sh
   a) Edit VERSION to 1.10 and execute the script "./goinstall.sh --darwin" for mac. Now install following packages if not present already.
   b) go get "github.com/gorilla/mux"
   c) go get "github.com/gorilla/context"
   d) go get "github.com/gorilla/handlers"
   e) go get "gopkg.in/mgo.v2"

   If you already have a go setup or $GOPATH , you can copy article-api/ folder into your $GOPATH/src folder which i will share.

2. You need to have docker and docker-compose installed, versions I used are mentioned at top.

3. Use "docker-compose up -d" to bring up two containers for mongo db and article-api app respectively. I have uploaded the article-api image to docker hub so that you can easily download and port my codebase easily anywhere.
4. Alternatively, you can execute "docker image build -t article-api ." to build image yourself. Please make sure office firewall is not blocing dns queries of docker build process. I had to edit daemon.json to add dns and restart docker engine. 

#cat /etc/docker/daemon.json
{
  "dns": ["192.168.1.6"]
}

If you face issues while building docker image , please try dns setting for docker engine. After building this image , you will need to edit docker-compose.yml image name and start docker-compose. Make sure port 27017 and 8000 are free. Your setup should be ready.

5. To start unit testing of this service , please check /article-api/tests/unit-tests/README.md

## Description of this solution

1. I have tried to adhere to the instructions given in the test link. Three endpoints have been developed.
   a) POST /articles
   b) GET /articles/{id}
   c) GET /tags/{tagName}/{date}

   Articles will only accept the fields given in test link i.e id, title, date, body, tags. Any other tag will be greeted with 400 bad request message. For getting the articles with a tag and date , I noticed that data format did not have hyphens, so this api will accept 20160922 and not 2016-09-22. As i said i have tried to follow the instructions as much as possible. For c) , I am crafting a custom response struct where articles field is a string array of last 10 ids of articles with tag and date. 

2. Even though I had zilch experience in Golang but the line-"submission written in Go will be looked on favourably" caught my eye. I took it as a challenge to develop API. I took help from various blogs, stackoverflow, github to learn and improve about myriads of roadblock I faced along the way. I had an high level idea of NoSQL and that probably helped in setting the DB. 
 a) Error handling:
    i)  GET request for non existent article ID will respond with 404 NOT FOUND.
    ii) POST request for articles with unknown and random fields will respond with 400 BAD REQUEST.
    iii)POST request for articles with existing article ID will respond with 409 CONFLICT.
    iv) GET request for tag&date  will respond with 404 NOT FOUND, if there is no such article with given tag and date.
 b) Testing strategy:
    i) Tried to follow test driven approach of writing unit test cases in parallel to developing api endpoints.
    ii) Logged few details to help in debugging.
    iii) Ran "go test -v" from /article-api folder (make sure you have installed go packages i mentioned above). I have used testing package to simulate unit tests. Kept improving after finding issues.
    iv) Installed mongoshell to manually login to mongodb and verify the documents in collections. 

3. Listing out the api calls for reference purpose, which you can make from local machine once you docker containers are up and running.
   a) curl -X POST http://localhost:8000/articles --data '{"id": "someuniqueid", "title": "latest science shows that potato chips are better for you than sugar", "date" : "2016-09-22", "tags" : ["health", "fitness", "media"]}' -v

   b) curl -X GET http://localhost:8000/articles/someuniqueid -v

   c) curl -X GET http://localhost:8000/tags/health/20160922 -v

 
## Assumptions

1. I have not done thorough negative testing and have tried to stick with specific desired functionality of these API endpoints.
2. I have done development on ubuntu machine and giving you docker image hoping the code will work fine for you on macOs.
3. Assuming the you have installed docker, docker compose and go setup as well.
4. You have internet connectivity :) 

## What I thought of this test and how long it took to complete?

  Let me start by telling that my schedule was really tight with a AWS Certified solutions architect-associate exam this monday, which I have passed. I started on this task on Saturday and gave it a day for understanding Golang and mongoDB. After that I worked on this task on monday and Tuesday so that should make it less than 3 days to complete this task. I thought the test was not difficult by itself but given my situation it was bit time taking, as I had to build from scratch. I had to do it the hard way which is one time effort of course. I can say that I understand Golang better now and would be able to make out basic flow of code. Once the coding was done, I quickly dockerised it because I remembered that one of the conditions was that program should work on macOS also. I have tried to dockerise the unit testing as well. I understand that there is lot of scope for improvement as a person should keep on improving, however given time constraints I could do this much only for now. I sincerely hope my efforts will push my case.



