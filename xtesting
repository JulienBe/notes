[doc](https://xtesting.readthedocs.io/en/latest/) & [pres](http://testresults.opnfv.org/functest/xtesting/)

[xtesting ONAP robot](https://github.com/Orange-OpenSource/xtesting-onap-robot)

# two files needed

### testcases.yaml

describes tests available in the container

Each test case has the following structure:
- case_name
- project_name: the project managing this test
- criteria: 0-100. if it's below that, the test is considered failed. 100 if you don't want to tolerate failure
- blocking: true or false. If true, will block the tests declared below
- run
- - name: test type, robotframework, python, bash,.. 
- - args: depens on the name (really, the type ?). For example: paths to the robot file, variablefile


### [Dockerfile](https://github.com/Orange-OpenSource/xtesting-onap-robot/blob/master/Dockerfile)

Used to setup what the will need in order to run. xtesting is already including robot, python, other stuff ?
`COPY testcases.yaml /usr/lib/python2.7/site-packages/xtesting/ci/testcases.yaml`
