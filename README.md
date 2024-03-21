Working queries
{container="backstage"} |=`\"level\":\"warn\"`
sum(count_over_time({container="backstage"} |~ "(Error|FATAL)" [$__auto]))


