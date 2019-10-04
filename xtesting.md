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



upload to db
```
...
    def __init__(self, **kwargs):
        self.details = {}
        self.project_name = kwargs.get('project_name', 'xtesting')
        self.case_name = kwargs.get('case_name', '')
        self.criteria = kwargs.get('criteria', 100)
        self.result = 0
        self.start_time = 0
        self.stop_time = 0
        self.is_skipped = False
...
    @decorators.can_dump_request_to_file
    def push_to_db(self):
        """Push the results of the test case to the DB.
        The following attributes must be set before pushing the results to DB:
            * project_name,
            * case_name,
            * result,
            * start_time,
            * stop_time.
        The next vars must be set in env:
            * TEST_DB_URL,
            * INSTALLER_TYPE,
            * DEPLOY_SCENARIO,
            * NODE_NAME,
            * BUILD_TAG.
        """
        try:
            if self.is_skipped:
                return TestCase.EX_PUSH_TO_DB_ERROR
            assert self.project_name
            assert self.case_name
            assert self.start_time
            assert self.stop_time
            url = env.get('TEST_DB_URL')
            data = {"project_name": self.project_name,
                    "case_name": self.case_name,
                    "details": self.details}
            data["installer"] = env.get('INSTALLER_TYPE')
            data["scenario"] = env.get('DEPLOY_SCENARIO')
            data["pod_name"] = env.get('NODE_NAME')
            data["build_tag"] = env.get('BUILD_TAG')
            data["criteria"] = 'PASS' if self.is_successful(
                ) == TestCase.EX_OK else 'FAIL'
            data["start_date"] = datetime.fromtimestamp(
                self.start_time).strftime('%Y-%m-%d %H:%M:%S')
            data["stop_date"] = datetime.fromtimestamp(
                self.stop_time).strftime('%Y-%m-%d %H:%M:%S')
            try:
                data["version"] = re.search(
                    TestCase._job_name_rule,
                    env.get('BUILD_TAG')).group(2)
            except Exception:  # pylint: disable=broad-except
                data["version"] = "unknown"
            req = requests.post(
                url, data=json.dumps(data, sort_keys=True),
                headers=self._headers)
```
