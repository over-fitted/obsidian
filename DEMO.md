# curl
`curl localhost:8080/movie/allCast/Avatar/Batman/1/7/15`
return list of node ids for cast path between the movies


# endpoints to show
`localhost:8080/path/allPath/573a139af29313caabcefb1d/573a1398f29313caabceb515/7/9/15`
allPathsBetweenNodesShowEdges

Find K-hops from a user
`curl localhost:8080/demo/allPaths/2/Amy_Ramirez`

# User Stories
## Story 2
`curl localhost:8080/demo/path/castNames/1/2/5/	Frank_Powell,Grace_Henderson`

# Story 4a
`curl localhost:8080/demo/path/nested/tomato/1/3/2/4.9`

# Story 4b
`curl localhost:8080/demo/path/nested/awards/1/3/2/100`

# Story 4c
`curl localhost:8080/demo/path/nested/rating/1/3/2/9`

# Story 5
`curl localhost:8080/demo/path/nested/awards/1/3/100/100/Leonardo_DiCaprio,Billy_Zane`

# Story 6
`curl localhost:8080/demo/allPaths/1/3/2`

# Story 7
`curl localhost:8080/demo/namedCastMovieCastPath/1/5/2/Kevin_Bacon/Kevin_Spacey`

# Story 8
`curl localhost:8080/demo/namedCastDirectPath/15/Martin_Fuller/Walter_Edwin`

# Story 9
`curl localhost:8080/demo/namedCastNamedMoviePath/1/3/15/Kevin_Spacey/Avatar`