# Goreplay-demo

## Pulling docker images

this demo is based of the docker image feiznouri/goreplay:1.1 for testing goreplay version 1.2.0 and the image feiznouri/python-server:1.1 to use it as a server.

pull the two images.

see the Goreplay wiki for more infos : https://github.com/buger/goreplay/wiki

## Capturing web traffic

run this command in terminal to start the server : ```docker run -it -p 3000:8000 feiznouri/python-server:1.1 8000``` 
the server will run on port 8000 and we'll map it to port 3000 in host.

then run this command : ```docker run -i -t --network=host feiznouri/goreplay:1.1 --input-raw :3000 --output-stdout```

This command says to listen for all network activity happening on port 8000 and log it to stdout.

Make a few requests by opening ```http://localhost:3000``` in your browser, or just by calling curl in terminal ```curl http://localhost:3000```. You should see that gor outputs all the HTTP requests right to the terminal window where it is running. Note that by default GoReplay do not track responses, you can enable them using ```--output-http-track-response``` option.

## Replaying

run this command in terminal to start another server : ```docker run -it -p 3001:8000 feiznouri/python-server:1.1 8000```

then run this command : ```docker run -i -t --network=host feiznouri/goreplay:1.1 --input-raw :3000 --output-http="http://localhost:3001"```

this command will replay the traffic in the first server to the second, make few requests to first server, you should see them replicated to the second one.

## Saving requests to file and replaying them later

run the first server as usual , then run the command : ```docker run -i -t  --mount type=bind,source=/home/feiz-nouri/goreplay_files,target=/home/goreplay/record_files --network=host feiznouri/goreplay:1.1 --input-raw :3000 --output-file=record_files/requests_docker.gor ```

This will create new file and continuously write all captured requests to it.

then to replay request from file : ```docker run -i -t  --mount type=bind,source=/home/feiz-nouri/goreplay_files,target=/home/goreplay/record_files --network=host feiznouri/goreplay:1.1 --input-file /home/goreplay/record_files/requests_docker_0.gor --output-http="http://localhost:3000" ```

You should see all the recorded requests coming to the server, and they will be replayed in the same order and with exactly same timing as they were recorded.

