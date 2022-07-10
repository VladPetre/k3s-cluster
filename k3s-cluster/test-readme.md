# Tests helper

## jmeter tests - cli

### GET
```
.\jmeter.bat -n -t C:\Projects\phd\jmeter-tests\ropt.jmx -l C:\Projects\phd\jmeter-tests\logs\rep-ropt.csv -e -o C:\Projects\phd\jmeter-tests\reports\ropt
.\jmeter.bat -n -t C:\Projects\phd\jmeter-tests\ropt-r.jmx -l C:\Projects\phd\jmeter-tests\logs\rep-ropt-r.csv -e -o C:\Projects\phd\jmeter-tests\reports\ropt-r
```

### SAVE
```
.\jmeter.bat -n -t C:\Projects\phd\jmeter-tests\ropt-post.jmx -l C:\Projects\phd\jmeter-tests\logs\rep-ropt-post.csv -e -o C:\Projects\phd\jmeter-tests\reports\ropt-post
.\jmeter.bat -n -t C:\Projects\phd\jmeter-tests\ropt-r-post.jmx -l C:\Projects\phd\jmeter-tests\logs\rep-ropt-r-post.csv -e -o C:\Projects\phd\jmeter-tests\reports\ropt-r-post
```

### UPDATE
```
.\jmeter.bat -n -t C:\Projects\phd\jmeter-tests\ropt-put.jmx -l C:\Projects\phd\jmeter-tests\logs\rep-ropt-put.csv -e -o C:\Projects\phd\jmeter-tests\reports\ropt-put
.\jmeter.bat -n -t C:\Projects\phd\jmeter-tests\ropt-r-put.jmx -l C:\Projects\phd\jmeter-tests\logs\rep-ropt-r-put.csv -e -o C:\Projects\phd\jmeter-tests\reports\ropt-r-put
```

### AIO (All-in-one)
```
.\jmeter.bat -n -t C:\Projects\phd\jmeter-tests\ropt-aio.jmx -l C:\Projects\phd\jmeter-tests\logs\rep-ropt-aio.csv -e -o C:\Projects\phd\jmeter-tests\reports\ropt-aio
.\jmeter.bat -n -t C:\Projects\phd\jmeter-tests\ropt-r-aio.jmx -l C:\Projects\phd\jmeter-tests\logs\rep-ropt-r-aio.csv -e -o C:\Projects\phd\jmeter-tests\reports\ropt-r-aio
```

## deploy services
```
kubectl apply -f .\ropt-receiver\deployment-service.yml
kubectl apply -f .\ropt-localizer\deployment-service.yml
kubectl apply -f .\ropt-receiver-reactive\deployment-service.yml
kubectl apply -f .\ropt-localizer-reactive\deployment-service.yml
```

kubectl scale --replicas=2 deployment/localizer -n ropt
kubectl scale --replicas=3 deployment/receiver -n ropt