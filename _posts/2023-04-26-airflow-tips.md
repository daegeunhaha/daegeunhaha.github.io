---
layout: post
title: Airflow study
subtitle: core concept of airflow
tags: [study, airflow]
comments: true
---

해당 post는 [airflow 공식 document](https://airflow.apache.org/docs/apache-airflow/2.4.3/index.html)를 공부하고, 내용을 요약한 것이다.  
당장 업무에 투입하게 위하여 공부하는 것이므로, broad하게 모든 내용을 공부하기보다는 당장 눈에 들어오는 필요한 내용 위주로 요약을 진행할 것이다.

### Tutorials

#### Fundamental Concepts

* Default Arguments
  각 task에 넘겨 줄 argument 중 공통적으로 들어가는 것은 default argument를 사용할 수 있다.
* Operator
  Airflow에서 하나의 work를 끝내는 단위이다.
* Task
  Airflow에서 Operator를 사용하기 위해서는 이를 task로 인스턴스화 해야 한다.
* DAG
  DAG object는 task들로 이루어진다.
* Templating with Jinja
  Airflow는 [Jinja Templating](https://jinja.palletsprojects.com/en/2.11.x/)을 활용한다.

#### Working with TaskFlow

Airflow 2.0부터 도입된 Taskflow에 대해 공부한다.

* Instantiate a DAG
  * DAG는 `@dag` decorator를 이용하여 생성한다.
  * python function name이 DAG identifier로서 동작한다.
  * dag를 invoke하려면, function을 run해주면 된다.
  * 2.4 버전부터는 `with` block 안에서 DAG가 사용되었거나, `@dag` decorator가 사용된 dag는 global variable로 등록할 필요 없다.
* Tasks
  * task는 `@task` decorator를 이용하여 생성한다.
  * returned value는 추후 다른 task에서 사용된다.
* Main flow of the DAG
  * DAG의 main flow는 DAG 안쪽에 정의한다.
  * `@task`로 decorated 된 python function들을 적절히 호출하는 것으로써 flow를 구성한다.
  * 마지막으로, `@dag`로 decorated 된 python function을 호출하는 것으로써 dag를 enable한다.
* Reusing a decorated task
  * `@dag`, `@task`로 decorate된 dag와 task를 import하여 다른 python file에서 reuse 할 수 있다.

#### Building a Running Pipeline

* connection에 미리 정보를 만들어 두어서 외부 connection이 있는 경우 이를 이용해서 할 수 있음
* hook을 이용하면 connection을 가지고 직접 쿼리를 해 볼 수 있음

#### Best Practices

새로운 DAG를 만드는 것은 다음의 step을 따른다.

1. DAG object를 만드는 python code를 짠다.
2. code가 우리의 expectation을 만족하는지 test한다.
3. DAG를 돌리기 위한 evironment dependency를 구성한다.

* Writing a DAG
  * Creating a task
    * task를 DB의 transaction으로 취급해야 한다. 따라서, incomplete한 result를 생성하지 않도록 해야 한다.
    * Airflow는 task가 실패했을 경우 여러번 재시도한다. task는 여러번 re-run하더라도 같은 결과 상태를 만들도록 설계되어야 한다.
      * INSERT문을 사용하는 대신 UPSERT를 사용하라
      * Read & Write로서 specific한 partition을 읽도록 한다. **절대로 latest available data를 읽지 않도록 한다.**
      * now()같은 현재 시간을 가져오는 함수는 task 안에서 사용하지 않도록 한다.
  * Communication
    * local file로서 중간 result를 저장하지 않는다.
    * task 간 통신은 XCom을 최대한 활용한다.
  * Top level Python Code
    * Operator나 Operator 간 DAG relationship을 만드는 데 필요한 것 외의 top level code는 지양한다.
      * 특정 task에서 쓰이는 module의 경우, 해당 task에서 직접 import해서 사용하도록 한다.
  * Airflow Variables
    * DAG의 top level Python code에서는 Airflow Variable을 사용하는 것을 지양하는 것이 좋다.
    * operator의 execute() method 안에서는 자유롭게 사용 가능하다.
    * 하지만 Jinja template를 사용하면 Airflow Variable을 operator에 전달할 수 있다. task execution 시점까지 value를 읽는 것을 delay할 수 있게 되기 때문이다.
  * Triggering DAGs after changes
    * DAG 파일을 바꾼 후에 바로 trigger를 하는 것을 지양하라.
    * update가 너무 느린 것 같다면, configuration parameter를 조절해볼 수 있다. (자세한 내용은 [document](create_employees_temp_table) 참고)
  * Example of watcher pattern with trigger rules
    * watcher pattern은 다른 task의 state를 watching하는 task를 trigger 하는 방법이다.
    * primary purpose는 다른 어떤 task가 fail할 때 DAG Run을 fail하게 하는 것이다.
    * 기본적으로는 어떤 task가 fail하건 전체 DAG Run은 fail status를 가지게 된다.
    * 하지만 trigger rule을 사용하면 normal running flow를 무시하고 새로운 status를 가지게 할 수 있다.
* Testing a DAG
  * DAG Loader Test
    * 이 테스트는 내 DAG가 loading 시에 error를 raise하지 않는지 확인하는 test이다.
    * 테스트를 돌리는 데 추가적인 code가 필요하지 않고, 그냥 python 파일을 run하면 된다.

      ```bash
      python your-dag-file.py
      ```

    * 해당 command를 통해 uninstalled dependency, syntax error 등을 체크해볼 수 있다.
    * time 명령어와 함께 DAG load가 예상만큼 빠르게 되는지 테스트해볼 수 있다.

      ```bash
      time python airflow/example_dags/example_python_operator.py
      ```

    * time 명령어를 사용할 때, initial interpreter startup time은 다음의 command를 통해 알 수 있다.

      ```bash
      time python -c ''
      ```

  * Unit tests
    * Unit test for loading a DAG

      ```python
      import pytest

      from airflow.models import DagBag

      @pytest.fixture()
      def dagbag():
          return DagBag()

      def test_dag_loaded(dagbag):
          dag = dagbag.get_dag(dag_id="hello_world")
          assert dagbag.import_errors == {}
          assert dag is not None
          assert len(dag.tasks) == 1
      ```

    * Unit test a DAG structure

      ```python
      def assert_dag_dict_equal(source, dag):
          assert dag.task_dict.keys() == source.keys()
          for task_id, downstream_list in source.items():
              assert dag.has_task(task_id)
              task = dag.get_task(task_id)
              assert task.downstream_task_ids = set(downstream_list)

      def test_dag():
          assert_dag_dict_equal(
              {
                  "DummyInstruction_0": ["DummyInstruction_1"],
                  "Dummyinstruction_1": ["DummyInstruction_2"],
                  "DummyInstruction_2": ["DummyInstruction_3"],
                  "DummyInstruction_3": [],
              },
              dag,
          ) 
      ```

    * Unit test for custom operator

      ```python
      import datetime

      import pendulum
      import pytest

      from airflow import DAG
      from airflow.utils.state import DagRunState, TaskInstanceState
      from airflow.utils.types import DagRunType

      DATA_INTERVAL_START = pendulum.datetime(2021, 9, 13, tz="UTC")
      DATA_INTERVAL_END = DATA_INTERVAL_START + datetime.timedelta(days=1)

      TEST_DAG_ID = "my_custom_operator_dag"
      TEST_TASK_ID = "my_custom_operator_task"

      @pytest.fixture()
      def dag():
          with DAG(
              dag_id=TEST_DAG_ID,
              schedule="@daily"
              start_date=DATE_INTERVAL_START,
          ) as dag:
              MyCustomOperator(
                  task_id=TEST_TASK_ID,
                  prefix="s3://bucket/some/prefix",
              )
          return dag

      def test_my_custom_operator_execute_no_trigger(dag):
          dagrun = dag.create_dagrun(
              state=DagRunState.RUNNING,
              executino_date=DATA_INTERVAL_START,
              data_interval=(DATA_INTERVAL_START, DATA_INTERVAL_END),
              start_date=DATA_INTERVAL_END,
              run_type=DagRunType.MANUAL,
          )
          ti = dagrun.get_task_instance(task_id=TEST_TASK_ID)
          ti.task = dag.get_task(task_id=TEST_TASK_ID)
          ti.run(ignore_ti_state=True)
          assert ti.state == TaskInstanceState.SUCCESS
          # Assert something related to tasks results.
      ```

  * Mocking variables and connections
    * variable에 대해서는 os.environ dict에서 AIRFLOW_VAR_{KEY}를 사용한다.
    * connection에 대해서는 os.environ dict에서 AIRFLOW_CONN_{CONN_ID}를 사용한다.
