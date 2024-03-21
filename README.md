Working queries

sum(count_over_time({container="backstage"} |~ "(Error|FATAL)" [1h]))

sum(count_over_time({container="backstage"} |=`\"level\":\"info\"` [1h]))

