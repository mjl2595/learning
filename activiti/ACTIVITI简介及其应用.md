#                                         ACTIVITI简介及其应用

## 1、表结构的含义

~~~visual basic
a、ACT_RE_*: 'RE'表示repository。 这个前缀的表包含了流程定义和流程静态资源 （图片，规则，等等）a、ACT_RE_*: 'RE'表示repository。 这个前缀的表包含了流程定义和流程静态资源 （图片，规则，等等）、ACT_RE_*: 'RE'表示repository。 这个前缀的表包含了流程定义和流程静态资源 （图片，规则，等等）a、ACT_RE_*: 'RE'表示repository。 这个前缀的表包含了流程定义和流程静态资源 （图片，规则，等等）。 
1、act_re_deployment   ---部署信息表

2、act_re_model  --流程设计模型部署表
关联键 ： DEPLOYMENT_ID_（activiti.act_re_deployment）  EDITOR_SOURCE_VALUE_ID_（activiti.act_ge_bytearray）   EDITOR_SOURCE_EXTRA_VALUE_ID_（activiti.act_ge_bytearray）

3、act_re_procdef  --流程定义数据表
联合主键：KEY_  VERSION_  TENANT_ID_  



b、ACT_RU_*: 'RU'表示runtime。 这些运行时的表，包含流程实例，任务，变量，异步任务，等运行中的数据。 Activiti只在流程实例执行过程中保存这些数据， 在流程结束时就会删除这些记录。 这样运行时表可以一直很小速度很快。
1、act_ru_event_subscr   
关联键： EXECUTION_ID_（activiti.act_ru_execution）

2、act_ru_execution  --运行时流程执行实例表
关联键： PROC_INST_ID_（activiti.act_ru_execution） 父任务  
PROC_DEF_ID_（activiti.act_re_procdef）
PROC_INST_ID_（activiti.act_ru_execution）
SUPER_EXEC_（activiti.act_ru_execution）

3、act_ru_identitylink  --运行时流程人员表  主要存储任务节点和参与者的相关信息
关联键： TASK_ID_（activiti.act_ru_task）  PROC_INST_ID_（activiti.act_ru_execution） PROC_DEF_ID_（activiti.act_re_procdef）

3、act_ru_job 
关联键：EXCEPTION_STACK_ID_（activiti.act_ge_bytearray）


4、act_ru_task   --运行时任务节点表
关联键： EXECUTION_ID_（activiti.act_ru_execution） PROC_INST_ID_（activiti.act_ru_execution）  PROC_DEF_ID_（activiti.act_re_procdef）

5、act_ru_variable  --运行时流程变量数据表
关联键： EXECUTION_ID_（activiti.act_ru_execution） PROC_INST_ID_（activiti.act_ru_execution） BYTEARRAY_ID_（activiti.act_ge_bytearray）


c、ACT_ID_*: 'ID'表示identity。 这些表包含身份信息，比如用户，组等等。

1、act_id_group  -- 用户组织表

2、act_id_info   --用户扩展信息表

3、act_id_membership  --用户与用户组映射表
联合主键：USER_ID_  GROUP_ID_ 

4、act_id_user  --用户信息表



d、ACT_HI_*: 'HI'表示history。 这些表包含历史数据，比如历史流程实例， 变量，任务等等。
1、act_hi_actinst   --历史节点表
PROC_DEF_ID_   PROC_INST_ID_   EXECUTION_ID_   ACT_ID_   TASK_ID_  

2、act_hi_attachment --历史附件表
USER_ID_  TASK_ID_   PROC_INST_ID_   CONTENT_ID_  

3、act_hi_comment  --历史意见表
USER_ID_   TASK_ID_  PROC_INST_ID_ 

4、act_hi_detail --历史详情表
PROC_INST_ID_   EXECUTION_ID_   TASK_ID_  ACT_INST_ID_   BYTEARRAY_ID_ 

5、act_hi_identitylink  --历史流程人员表
USER_ID_   TASK_ID_   PROC_INST_ID_   GROUP_ID_

6、 act_hi_procinst  --历史流程实例表
PROC_INST_ID_   BUSINESS_KEY_  PROC_DEF_ID_  START_USER_ID_   START_ACT_ID_   END_ACT_ID_   SUPER_PROCESS_INSTANCE_ID_   TENANT_ID_

7、act_hi_taskinst  --历史任务实例表
PROC_DEF_ID_   TASK_DEF_KEY_   PROC_INST_ID_    EXECUTION_ID_   PARENT_TASK_ID_   TENANT_ID_ 

8、act_hi_varinst  --历史变量表
PROC_INST_ID_   EXECUTION_ID_  TASK_ID_   BYTEARRAY_ID_ 


e、ACT_GE_*: 'GE'表示general。通用数据， 用于不同场景下，如存放资源文件。
act_ge_bytearray 
关联键   DEPLOYMENT_ID_（activiti.act_re_deployment）

act_ge_property

f、act_evt_log 
PROC_DEF_ID_  PROC_INST_ID_  EXECUTION_ID_   TASK_ID_   USER_ID_  


g、act_procdef_info  
PROC_DEF_ID_ （activiti.act_re_procdef）   INFO_JSON_ID_ （activiti.act_ge_bytearray）

~~~

## 2、Service对象

~~~visual basic

~~~

RepositoryService----操作静态的资源（流程定义，bpmn、png）

RuntimeService-----操作流程实例（启动流程实例、查询流程实例、结束流程实例）

TaskService-----操作任务（查询任务、办理任务）

HistoryService----操作历史数据



## 3、Activiti框架提供的对象(和表有对应关系)

~~~visual basic
Deployment-----act_re_deployment       流程部署

ProcessDefinition----act_re_procdef    流程定义

ProcessInstance-----act_ru_execution   流程实例
~~~

## 4、工作流程图

![api.services](https://www.activiti.org/userguide/images/api.services.png)

~~~java
ProcessEngine processEngine = ProcessEngines.getDefaultProcessEngine();

RuntimeService runtimeService = processEngine.getRuntimeService();
RepositoryService repositoryService = processEngine.getRepositoryService();
TaskService taskService = processEngine.getTaskService();
ManagementService managementService = processEngine.getManagementService();
IdentityService identityService = processEngine.getIdentityService();
HistoryService historyService = processEngine.getHistoryService();
FormService formService = processEngine.getFormService();
DynamicBpmnService dynamicBpmnService = processEngine.getDynamicBpmnService();

FormService 是一个可选服务。这意味着Activiti完全可以在不需要它的情况下使用，而不需要牺牲任何功能。该服务引入了开始表单和任务表单的概念。开始表单是在流程实例启动之前显示给用户的表单，而任务表单是当用户想要完成表单时显示的表单。Activiti允许在BPMN 2.0流程定义中定义这些表单。此服务以一种易于使用的方式公开此数据。但是，这是可选的，因为表单不需要嵌入到流程定义中。

IdentityService非常简单。它允许管理组和用户(创建、更新、删除、查询……)。重要的是要理解Activiti实际上并不在运行时对用户进行任何检查。例如，可以将任务分配给任何用户，但是引擎不会验证该用户是否为系统所知。这是因为Activiti引擎还可以与LDAP、Active Directory等服务一起使用。

HistoryService公开由Activiti引擎收集的所有历史数据。在执行流程时，引擎可以保存大量数据(这是可配置的)，例如流程实例启动时间、谁执行了哪些任务、完成任务所需的时间、每个流程实例中遵循的路径等。此服务主要公开访问此数据的查询功能。

在使用Activiti编码自定义应用程序时，通常不需要ManagementService。它允许检索关于数据库表和表元数据的信息。此外，它还公开作业的查询功能和管理操作。作业在Activiti中用于各种事情，如计时器、异步延续、延迟暂停/激活等。稍后，将更详细地讨论这些主题。

可以使用DynamicBpmnService更改流程定义的一部分，而不需要重新部署它。例如，您可以更改流程定义中的用户任务的assignee定义，或者更改服务任务的类名

~~~

### 

## 5、部署流程

![api.vacationRequest](https://www.activiti.org/userguide/images/api.vacationRequest.png)

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<definitions id="definitions"
             targetNamespace="http://activiti.org/bpmn20"
             xmlns="http://www.omg.org/spec/BPMN/20100524/MODEL"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:activiti="http://activiti.org/bpmn">

  <process id="vacationRequest" name="Vacation request">

    <startEvent id="request" activiti:initiator="employeeName">
      <extensionElements>
        <activiti:formProperty id="numberOfDays" name="Number of days" type="long" value="1" required="true"/>
        <activiti:formProperty id="startDate" name="First day of holiday (dd-MM-yyy)" datePattern="dd-MM-yyyy hh:mm" type="date" required="true" />
        <activiti:formProperty id="vacationMotivation" name="Motivation" type="string" />
      </extensionElements>
    </startEvent>
    <sequenceFlow id="flow1" sourceRef="request" targetRef="handleRequest" />

    <userTask id="handleRequest" name="Handle vacation request" >
      <documentation>
        ${employeeName} would like to take ${numberOfDays} day(s) of vacation (Motivation: ${vacationMotivation}).
      </documentation>
      <extensionElements>
         <activiti:formProperty id="vacationApproved" name="Do you approve this vacation" type="enum" required="true">
          <activiti:value id="true" name="Approve" />
          <activiti:value id="false" name="Reject" />
        </activiti:formProperty>
        <activiti:formProperty id="managerMotivation" name="Motivation" type="string" />
      </extensionElements>
      <potentialOwner>
        <resourceAssignmentExpression>
          <formalExpression>management</formalExpression>
        </resourceAssignmentExpression>
      </potentialOwner>
    </userTask>
    <sequenceFlow id="flow2" sourceRef="handleRequest" targetRef="requestApprovedDecision" />

    <exclusiveGateway id="requestApprovedDecision" name="Request approved?" />
    <sequenceFlow id="flow3" sourceRef="requestApprovedDecision" targetRef="sendApprovalMail">
      <conditionExpression xsi:type="tFormalExpression">${vacationApproved == 'true'}</conditionExpression>
    </sequenceFlow>

    <task id="sendApprovalMail" name="Send confirmation e-mail" />
    <sequenceFlow id="flow4" sourceRef="sendApprovalMail" targetRef="theEnd1" />
    <endEvent id="theEnd1" />

    <sequenceFlow id="flow5" sourceRef="requestApprovedDecision" targetRef="adjustVacationRequestTask">
      <conditionExpression xsi:type="tFormalExpression">${vacationApproved == 'false'}</conditionExpression>
    </sequenceFlow>

    <userTask id="adjustVacationRequestTask" name="Adjust vacation request">
      <documentation>
        Your manager has disapproved your vacation request for ${numberOfDays} days.
        Reason: ${managerMotivation}
      </documentation>
      <extensionElements>
        <activiti:formProperty id="numberOfDays" name="Number of days" value="${numberOfDays}" type="long" required="true"/>
        <activiti:formProperty id="startDate" name="First day of holiday (dd-MM-yyy)" value="${startDate}" datePattern="dd-MM-yyyy hh:mm" type="date" required="true" />
        <activiti:formProperty id="vacationMotivation" name="Motivation" value="${vacationMotivation}" type="string" />
        <activiti:formProperty id="resendRequest" name="Resend vacation request to manager?" type="enum" required="true">
          <activiti:value id="true" name="Yes" />
          <activiti:value id="false" name="No" />
        </activiti:formProperty>
      </extensionElements>
      <humanPerformer>
        <resourceAssignmentExpression>
          <formalExpression>${employeeName}</formalExpression>
        </resourceAssignmentExpression>
      </humanPerformer>
    </userTask>
    <sequenceFlow id="flow6" sourceRef="adjustVacationRequestTask" targetRef="resendRequestDecision" />

    <exclusiveGateway id="resendRequestDecision" name="Resend request?" />
    <sequenceFlow id="flow7" sourceRef="resendRequestDecision" targetRef="handleRequest">
      <conditionExpression xsi:type="tFormalExpression">${resendRequest == 'true'}</conditionExpression>
    </sequenceFlow>

     <sequenceFlow id="flow8" sourceRef="resendRequestDecision" targetRef="theEnd2">
      <conditionExpression xsi:type="tFormalExpression">${resendRequest == 'false'}</conditionExpression>
    </sequenceFlow>
    <endEvent id="theEnd2" />

  </process>

</definitions>
~~~

~~~java
ProcessEngine processEngine = ProcessEngines.getDefaultProcessEngine();
RepositoryService repositoryService = processEngine.getRepositoryService();
repositoryService.createDeployment()
  .addClasspathResource("org/activiti/test/VacationRequest.bpmn20.xml")
  .deploy();

Log.info("Number of process definitions: " + repositoryService.createProcessDefinitionQuery().count());
~~~

## 6、启动流程实例

~~~java
Map<String, Object> variables = new HashMap<String, Object>();
variables.put("employeeName", "Kermit");
variables.put("numberOfDays", new Integer(4));
variables.put("vacationMotivation", "I'm really tired!");

RuntimeService runtimeService = processEngine.getRuntimeService();
ProcessInstance processInstance = runtimeService.startProcessInstanceByKey("vacationRequest", variables);

// Verify that we started a new process instance
Log.info("Number of process instances: " + runtimeService.createProcessInstanceQuery().count());
~~~

## 7、处理任务

根据组名列出所有待执行的任务

~~~java
// Fetch all tasks for the management group
TaskService taskService = processEngine.getTaskService();
List<Task> tasks = taskService.createTaskQuery().taskCandidateGroup("management").list();
for (Task task : tasks) {
  Log.info("Task available: " + task.getName());
}
~~~

获取一个任务 处理任务

~~~java
Task task = tasks.get(0);

Map<String, Object> taskVariables = new HashMap<String, Object>();
taskVariables.put("vacationApproved", "false");
taskVariables.put("managerMotivation", "We have a tight deadline!");
taskService.complete(task.getId(), taskVariables);
~~~

## 8、挂起和激活流程

~~~java
repositoryService.suspendProcessDefinitionByKey("vacationRequest");
try {
  runtimeService.startProcessInstanceByKey("vacationRequest");
} catch (ActivitiException e) {
  e.printStackTrace();
}

//runtimeService.suspendProcessInstance method.
   // runtimeService.activateProcessInstanceXXX
~~~

## 9、查询API

~~~java
//高级查询
List<Task> tasks = taskService.createTaskQuery()
    .taskAssignee("kermit")
    .processVariableValueEquals("orderId", "0815")  //给变量 赋值
    .orderByDueDate().asc()
    .list();
//低级或者原生查询
List<Task> tasks = taskService.createNativeTaskQuery()
  .sql("SELECT count(*) FROM " + managementService.getTableName(Task.class) + " T WHERE T.NAME_ = #{taskName}")
  .parameter("taskName", "gonzoTask")
  .list();

long count = taskService.createNativeTaskQuery()
  .sql("SELECT count(*) FROM " + managementService.getTableName(Task.class) + " T1, "
    + managementService.getTableName(VariableInstanceEntity.class) + " V1 WHERE V1.TASK_ID_ = T1.ID_")
  .count();

~~~

## 10、源码解析

![img](https://upload-images.jianshu.io/upload_images/2242271-4becaf2056d3c51f.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

~~~visual basic
CommandInterceptor
org.activiti.engine.impl.interceptor.LogInterceptor@750cd36d
-》》》
org.activiti.spring.SpringTransactionInterceptor

T result = transactionTemplate.execute(new TransactionCallback<T>() {
            public T doInTransaction(TransactionStatus status) {
                return next.execute(config, command);
            }
        });

result = action.doInTransaction(status);
org.activiti.engine.impl.interceptor.CommandContextInterceptor

org.activiti.engine.impl.interceptor.CommandInvoker

org.activiti.engine.impl.interceptor.CommandContext

~~~

~~~java
protected void init() {
  	initConfigurators();  
  	configuratorsBeforeInit();
    initProcessDiagramGenerator();
    initHistoryLevel();  --
    initExpressionManager();
    initDataSource();
    initVariableTypes();
    initBeans();
    initFormEngines();
    initFormTypes();
    initScriptingEngines();
    initClock();
    initBusinessCalendarManager();
    initCommandContextFactory();
    initTransactionContextFactory();
    initCommandExecutors();
    initServices();
    initIdGenerator();
    initDeployers();
    initJobHandlers();
    initJobExecutor();
    initAsyncExecutor();
    initTransactionFactory();
    initSqlSessionFactory();
    initSessionFactories();
    initJpa();
    initDelegateInterceptor();
    initEventHandlers();
    initFailedJobCommandFactory();
    initEventDispatcher();
    initProcessValidator();
    initDatabaseEventLogging();
    configuratorsAfterInit();
  }
1、initConfigurators();//初始化配置文件组件
   protected List<ProcessEngineConfigurator> configurators; // The injected configurators
   protected List<ProcessEngineConfigurator> allConfigurators; // Including auto-discovered configurators
2、configuratorsBeforeInit();  
  实现 ProcessEngineConfigurator
   public void beforeInit(ProcessEngineConfigurationImpl processEngineConfiguration) {
    
  }空实现
3、 initProcessDiagramGenerator();  //初始化流程图绘制生成器
    processDiagramGenerator = new DefaultProcessDiagramGenerator();
4、 initHistoryLevel(); //初始化历史等级 是否往历史表中写数据
5、 initExpressionManager(); //初始化表达式
6、 initDataSource(); 初始化数据源
7、 initVariableTypes(); 初始化变量类型
8、 initBeans();初始化beans
9、 initFormEngines(); 
10、initFormTypes(); 
11、initScriptingEngines();
12、initClock();
13、initBusinessCalendarManager();
14、initCommandContextFactory(); 
15、initTransactionContextFactory(); 
~~~

## 11、流程定义的版本

对于业务文档中每一个的流程定义，都会通过下列部署执行初始化属性`key`, `version`, `name` 和 `id`:

- XML文件中流程定义（流程模型）的 `id`属性被当做是流程定义的 `key`属性。
- XML文件中的流程模型的`name` 属性被当做是流程定义的 `name` 属性。如果该name属性并没有指定，那么id属性被当做是name。
- 带有特定key的流程定义在第一次部署的时候，将会自动分配版本号为1，对于之后部署相同key的流程定义时候，这次部署的版本号将会设置为比当前最大的版本号大1的值。该key属性被用来区别不同的流程定义。
- 流程定义中的id属性被设置为 {processDefinitionKey}:{processDefinitionVersion}:{generated-id}, 这里的`generated-id`是一个唯一的数字被添加，用于确保在集群环境中缓存的流程定义的唯一性。

## 12、 拾取任务



## 13、源码解析



CommandExecutor（命令执行器，用于执行命令），CommandInterceptor（命令拦截器，用于构建拦截器链），Command（命令自身）

~~~visual basic
Command的接口中只有一个execute方法，这里才是写命令的具体实现，而CommandExecutor的实现类在上面已经给出，其包含了一个CommandConfig和一个命令拦截器CommandInterceptor，而执行的execute(command)方法，实际上调用的就是commandInterceptor.execute(commandConfig,command)。CommandInterceptor中包含了一个set和get方法，用于设置next(实际上就是下一个CommandInterceptor)变量。想象一下，这样就能够通过这种形式找到拦截器链的下一个拦截器链，就可以将命令传递下去。
    
    
简单梳理一下：Service实现服务的其中一个标准方法是在具体服务中调用commandExecutor.execute(new command())(这里的command是具体的命令)。其执行步骤就是命令执行器commandExecutor.execute调用了其内部变量CommandInterceptor first（第一个命令拦截器）的execute方法（加上了参数commandConfig）。CommandInterceptor类中包含了一个CommandInterceptor对象next，用于指向下一个CommandInterceptor，在拦截器的execute方法中，只需要完成其对应的相关操作，然后执行一下next.execute(commandConfig,command)，就可以很简单的将命令传递给下一个命令拦截器，然后在最后一个拦截器中执行command.execute()，调用这个命令最终要实现的内容就行了。    


实现一个自定义的命令只需要实现Command<T>接口，在execute中做相应的操作就行了，而实现一个自定义的命令拦截器需要继承AbstractCommandInterceptor，在execute中做相应的处理，最后调用next.execute()即可，而命令执行器虽然也可以自己实现，但是没有多大意义，非常麻烦。前面说过，命令执行器会先执行命令拦截器链的execute方法，但命令拦截器链是如何构建的，命令又是在哪里调用的，第一个拦截器是如何添加到命令执行器的，这些都要关注于Activiti工作流引擎的初始化。


初始化的方法主要写在了org.activiti.engine.impl.cfg.ProcessEngineConfigurationImpl类的init()方法中，这里主要关注于其中的initCommandExecutors()，如果对activiti的配置不清楚的，可以好好的了解一下这个初始化过程。

　　initCommandExecutors():


[org.activiti.engine.impl.interceptor.LogInterceptor@26ca12a2,
org.activiti.spring.SpringTransactionInterceptor@afee63,
org.activiti.engine.impl.interceptor.CommandContextInterceptor@70d3cdbf, 
org.activiti.engine.impl.interceptor.CommandInvoker@10bf1ec9]
    
~~~

![img](https://upload-images.jianshu.io/upload_images/2242271-e6a84ab68b549bf4.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)



![IMG20200527-151657526](D:\mygithublearning\learning\activiti\IMG20200527-151657526.jpeg)

customPreCommandInterceptors->LogInterceptor->TransactionInterceptor->CommandContextInterceptor->customPostCommandInterceptors->commandInvoker

## 14、流程部署

![img](https://upload-images.jianshu.io/upload_images/2242271-bc6f111998288acc.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

```css
processEngine.getRepositoryService().createDeployment().addClasspathResource("hello.bpmn20.xml").deploy();
```

## 15、CommandContext

![img](https://upload-images.jianshu.io/upload_images/2242271-dbf9c4a66c79d3c9.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

## 15 、debug这个流程及其分析细节



```java
ProcessEngineFactoryBean implements FactoryBean<ProcessEngine> {
    protected ProcessEngineConfigurationImpl processEngineConfiguration;

    protected ApplicationContext applicationContext;
    protected ProcessEngine processEngine;

public ProcessEngine getObject() throws Exception {
            configureExpressionManager();
            configureExternallyManagedTransactions();

            if (processEngineConfiguration.getBeans() == null) {
                processEngineConfiguration.setBeans(new SpringBeanFactoryProxyMap(applicationContext));
            }

            this.processEngine = processEngineConfiguration.buildProcessEngine();
            return this.processEngine;
   }
}
      
```
~~~java
     SpringProcessEngineConfiguration extends ProcessEngineConfigurationImpl
     buildProcessEngine(
         ProcessEngine processEngine = super.buildProcessEngine();
        ProcessEngines.setInitialized(true);
        autoDeployResources(processEngine);
        return processEngine;
     );
~~~

~~~java

protected void initCommandInterceptors() {
    if (commandInterceptors==null) {
      commandInterceptors = new ArrayList<CommandInterceptor>();
      if (customPreCommandInterceptors!=null) {
        commandInterceptors.addAll(customPreCommandInterceptors);
      }
      commandInterceptors.addAll(getDefaultCommandInterceptors());
      if (customPostCommandInterceptors!=null) {
        commandInterceptors.addAll(customPostCommandInterceptors);
      }
      commandInterceptors.add(commandInvoker);
    }
  }

protected Collection< ? extends CommandInterceptor> getDefaultCommandInterceptors() {
    List<CommandInterceptor> interceptors = new ArrayList<CommandInterceptor>();
    interceptors.add(new LogInterceptor());
    
    CommandInterceptor transactionInterceptor = createTransactionInterceptor();
    if (transactionInterceptor != null) {
      interceptors.add(transactionInterceptor);
    }
    
    interceptors.add(new CommandContextInterceptor(commandContextFactory, this));
    return interceptors;
  }

 [org.activiti.engine.impl.interceptor.LogInterceptor@5d66ae3a, 
  org.activiti.spring.SpringTransactionInterceptor@30159886, 
  org.activiti.engine.impl.interceptor.CommandContextInterceptor@64a1116a, 
  org.activiti.engine.impl.interceptor.CommandInvoker@11e17893]
  //封装链对象

protected CommandInterceptor initInterceptorChain(List<CommandInterceptor> chain) {
    if (chain==null || chain.isEmpty()) {
      throw new ActivitiException("invalid command interceptor chain configuration: "+chain);
    }
    for (int i = 0; i < chain.size()-1; i++) {
      chain.get(i).setNext( chain.get(i+1) );
    }
    return chain.get(0);
  }
~~~



流程定义    -----多个执行实例  --  任务实例



ProcessEngine