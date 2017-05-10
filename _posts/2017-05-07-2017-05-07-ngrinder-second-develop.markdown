---
title: 2017-05-07-ngrinder-second-develop
layout: post
date: 2017-05-07 21:04:32  +0800 
categories: [second-develop]
tags: [ngrinder]
---


* content
{:toc}

                                                                                                                                                   
本文介绍 ngrinder源码解析











#### core code

##### controller

PrefTestRunnable.java
``` java
    /**
     * Run the given test.
     * <p/>
     * If fails, it marks STOP_BY_ERROR in the given {@link PerfTest} status
     *
     * @param perfTest perftest instance;
     */
    public void doTest(final PerfTest perfTest) {
        SingleConsole singleConsole = null;
        try {
            singleConsole = startConsole(perfTest);
            // prepare distribute groovy maven projet build
            ScriptHandler prepareDistribution = perfTestService.prepareDistribution(perfTest);
            // generate the properties for agent include runtime properties
            GrinderProperties grinderProperties = perfTestService.getGrinderProperties(perfTest, prepareDistribution);
            // agent start with properties
            startAgentsOn(perfTest, grinderProperties, checkCancellation(singleConsole));
            // distribute files to agents
            distributeFileOn(perfTest, checkCancellation(singleConsole));
            // report path
            singleConsole.setReportPath(perfTestService.getReportFileDirectory(perfTest));
            // start test with properties
            runTestOn(perfTest, grinderProperties, checkCancellation(singleConsole));
        } catch (SingleConsoleCancellationException ex) {
            // In case of error, mark the occurs error on perftest.
            doCancel(perfTest, singleConsole);
            notifyFinish(perfTest, StopReason.CANCEL_BY_USER);
        } catch (Exception e) {
            // In case of error, mark the occurs error on perftest.
            LOG.error("Error while executing test: {} - {} ", perfTest.getTestIdentifier(), e.getMessage());
            LOG.debug("Stack Trace is : ", e);
            doTerminate(perfTest, singleConsole);
            notifyFinish(perfTest, StopReason.ERROR_WHILE_PREPARE);
        }
    }
```


PrefTestService.java
``` java
    /**
     * Prepare files for distribution. This method stores the files on the path
     * ${NGRINDER_HOME}/perftest/{test_id}/dist folder.
     *
     * @param perfTest perfTest
     * @return File location in which the perftest script and resources are distributed.
     */
    public ScriptHandler prepareDistribution(PerfTest perfTest) {
        File perfTestDistDirectory = getDistributionPath(perfTest);
        User user = perfTest.getCreatedUser();
        FileEntry scriptEntry = checkNotNull(fileEntryService.getOne(user, checkNotEmpty(perfTest.getScriptName(), "perfTest should have script name"), getSafe(perfTest.getScriptRevision())), "script should exist");
        // Get all files in the script path
        ScriptHandler handler = scriptHandlerFactory.getHandler(scriptEntry);

        ProcessingResultPrintStream processingResult = new ProcessingResultPrintStream(new ByteArrayOutputStream());
        // buildmaven project maven clean complie package
        handler.prepareDist(perfTest.getId(), user, scriptEntry, perfTestDistDirectory, config.getControllerProperties(), processingResult);
        // zip dist directory
        try {
            CompressionUtils.zip(perfTestDistDirectory);
        } catch (IOException e) {
            throw processException("Error while file zip perfTestDistDirectory.");
        }
        LOGGER.info("File write is completed in {}", perfTestDistDirectory);
        if (!processingResult.isSuccess()) {
            File logDir = new File(getLogFileDirectory(perfTest), "distribution_log.txt");
            try {
                FileUtils.writeByteArrayToFile(logDir, processingResult.getLogByteArray());
            } catch (IOException e) {
                noOp();
            }
            throw processException("Error while file distribution is prepared.");
        }
        return handler;
    }
```

PerfTestService.java line:560
``` java
  /**
   * Create {@link GrinderProperties} based on the passed {@link PerfTest}.
   *
   * @param perfTest      base data
   * @param scriptHandler scriptHandler
   * @return created {@link GrinderProperties} instance
   */
  public GrinderProperties getGrinderProperties(PerfTest perfTest, ScriptHandler scriptHandler) {
    try {
      // Use default properties first
      GrinderProperties grinderProperties = new GrinderProperties(config.getHome().getDefaultGrinderProperties());

      User user = perfTest.getCreatedUser();

      // Get all files in the script path
      String scriptName = perfTest.getScriptName();
      FileEntry userDefinedGrinderProperties = fileEntryService.getOne(user, FilenameUtils.concat(FilenameUtils.getPath(scriptName), DEFAULT_GRINDER_PROPERTIES), -1L);
      if (!config.isSecurityEnabled() && userDefinedGrinderProperties != null) {
        // Make the property overridden by user property.
        GrinderProperties userProperties = new GrinderProperties();
        userProperties.load(new StringReader(userDefinedGrinderProperties.getContent()));
        grinderProperties.putAll(userProperties);
      }
      grinderProperties.setAssociatedFile(new File(DEFAULT_GRINDER_PROPERTIES));
      grinderProperties.setProperty(GRINDER_PROP_SCRIPT, scriptHandler.getScriptExecutePath(scriptName));

      grinderProperties.setProperty(GRINDER_PROP_TEST_ID, "test_" + perfTest.getId());
      grinderProperties.setInt(GRINDER_PROP_AGENTS, getSafe(perfTest.getAgentCount()));
      grinderProperties.setInt(GRINDER_PROP_PROCESSES, getSafe(perfTest.getProcesses()));
      grinderProperties.setInt(GRINDER_PROP_THREAD, getSafe(perfTest.getThreads()));
      if (perfTest.isThresholdDuration()) {
        grinderProperties.setLong(GRINDER_PROP_DURATION, getSafe(perfTest.getDuration()));
        grinderProperties.setInt(GRINDER_PROP_RUNS, 0);
      } else {
        grinderProperties.setInt(GRINDER_PROP_RUNS, getSafe(perfTest.getRunCount()));
        if (grinderProperties.containsKey(GRINDER_PROP_DURATION)) {
          grinderProperties.remove(GRINDER_PROP_DURATION);
        }
      }
      grinderProperties.setProperty(GRINDER_PROP_ETC_HOSTS, StringUtils.defaultIfBlank(perfTest.getTargetHosts(), ""));
      grinderProperties.setBoolean(GRINDER_PROP_USE_CONSOLE, true);
      if (BooleanUtils.isTrue(perfTest.getUseRampUp())) {
        grinderProperties.setBoolean(GRINDER_PROP_THREAD_RAMPUP, perfTest.getRampUpType() == RampUp.THREAD);
        grinderProperties.setInt(GRINDER_PROP_PROCESS_INCREMENT, getSafe(perfTest.getRampUpStep()));
        grinderProperties.setInt(GRINDER_PROP_PROCESS_INCREMENT_INTERVAL, getSafe(perfTest.getRampUpIncrementInterval()));
        if (perfTest.getRampUpType() == RampUp.PROCESS) {
          grinderProperties.setInt(GRINDER_PROP_INITIAL_SLEEP_TIME, getSafe(perfTest.getRampUpInitSleepTime()));
        } else {
          grinderProperties.setInt(GRINDER_PROP_INITIAL_THREAD_SLEEP_TIME, getSafe(perfTest.getRampUpInitSleepTime()));
        }
        grinderProperties.setInt(GRINDER_PROP_INITIAL_PROCESS, getSafe(perfTest.getRampUpInitCount()));
      } else {
        grinderProperties.setInt(GRINDER_PROP_PROCESS_INCREMENT, 0);
      }
      grinderProperties.setInt(GRINDER_PROP_REPORT_TO_CONSOLE, 500);
      grinderProperties.setProperty(GRINDER_PROP_USER, perfTest.getCreatedUser().getUserId());
      // set grinder.jvm.classpath for agent run
      grinderProperties.setProperty(GRINDER_PROP_JVM_CLASSPATH, getCustomClassPath(perfTest));
      grinderProperties.setInt(GRINDER_PROP_IGNORE_SAMPLE_COUNT, getSafe(perfTest.getIgnoreSampleCount()));
      grinderProperties.setBoolean(GRINDER_PROP_SECURITY, config.isSecurityEnabled());
      // For backward agent compatibility.
      // If the security is not enabled, pass it as jvm argument.
      // If enabled, pass it to grinder.param. In this case, I drop the
      // compatibility.
      if (StringUtils.isNotBlank(perfTest.getParam())) {
        String param = perfTest.getParam().replace("'", "\\'").replace(" ", "");
        if (config.isSecurityEnabled()) {
          grinderProperties.setProperty(GRINDER_PROP_PARAM, StringUtils.trimToEmpty(param));
        } else {
          String property = grinderProperties.getProperty(GRINDER_PROP_JVM_ARGUMENTS, "");
          property = property + " -Dparam=" + param + " ";
          // set grinder.jvm.arguments for agent run
          grinderProperties.setProperty(GRINDER_PROP_JVM_ARGUMENTS, property);
        }
      }
      LOGGER.info("Grinder Properties : {} ", grinderProperties);
      return grinderProperties;
    } catch (Exception e) {
      throw processException("error while prepare grinder property for " + perfTest.getTestName(), e);
    }
  }
```
GroovyMavenProjectScriptHandler.java
``` java
@Override
    protected void prepareDistMore(Long testId, User user, FileEntry script, File distDir, PropertiesWrapper properties, ProcessingResultPrintStream processingResult) {
        String pomPathInSVN = PathUtils.join(getBasePath(script), "pom.xml");
        MavenCli cli = new MavenCli();
        processingResult.println("\nCopy dependencies by running 'mvn dependency:copy-dependencies"+ " -DoutputDirectory=./lib -DexcludeScope=provided'");
        // excute maven commond
        int result = cli.doMain(new String[]{ // goal specification
                "dependency:copy-dependencies", // run dependency goal
                "-DoutputDirectory=./lib", // to the lib folder
                "-DexcludeScope=provided" // but exclude the provided
                // library
        }, distDir.getAbsolutePath(), processingResult, processingResult);
        boolean success = (result == 0);
        if (success) {
            processingResult.printf("\nDependencies in %s was copied.\n", pomPathInSVN);
            LOGGER.info("Dependencies in {} is copied into {}/lib folder", pomPathInSVN, distDir.getAbsolutePath());
        } else {
            processingResult.printf("\nDependencies copy in %s is failed.\n", pomPathInSVN);
            LOGGER.info("Dependencies copy in {} is failed.", pomPathInSVN);
        }
        // Then it's not necessary to include pom.xml anymore.
        FileUtils.deleteQuietly(new File(distDir, "pom.xml"));
        processingResult.setSuccess(result == 0);
    }
```

PrefTestRunnable.java
``` java

    /**
     * Distribute files to agents.
     *
     * @param perfTest      perftest
     * @param singleConsole console to be used.
     */
    void distributeFileOn(final PerfTest perfTest, SingleConsole singleConsole) {
        // Distribute files
        perfTestService.markStatusAndProgress(perfTest, DISTRIBUTE_FILES, "All necessary files are being distributed.");
        ListenerSupport<SingleConsole.FileDistributionListener> listener = ListenerHelper.create();
        final long safeThreadHold = getSafeTransmissionThreshold();

        listener.add(new SingleConsole.FileDistributionListener() {
            @Override
            public void distributed(String fileName) {
                perfTestService.markProgress(perfTest, " - " + fileName);
            }

            @Override
            public boolean start(File dir, boolean safe) {
                if (safe) {
                    perfTestService.markProgress(perfTest, "Safe file distribution mode is enabled.");
                    return safe;
                }
                long sizeOfDirectory = FileUtils.sizeOfDirectory(dir);
                if (sizeOfDirectory > safeThreadHold) {
                    perfTestService.markProgress(perfTest, "The total size of distributed files is over "+ UnitUtils.byteCountToDisplaySize(safeThreadHold) + "B.\n- Safe file distribution mode is enabled by force.");
                    return true;
                }
                return safe;
            }

        });

        // the files have prepared before
        // start distribute files
        singleConsole.distributeFiles(perfTestService.getDistributionPath(perfTest), listener, isSafeDistPerfTest(perfTest));
        perfTestService.markStatusAndProgress(perfTest, DISTRIBUTE_FILES_FINISHED, "All necessary files are distributed.");
    }
```

SingleConsole.java
``` java
    /**
     * Distribute files on agents.
     *
     * @param listener listener
     * @param safe     safe mode
     */
    public void distributeFiles(ListenerSupport<FileDistributionListener> listener, final boolean safe) {
        final FileDistribution fileDistribution = getConsoleComponent(FileDistribution.class);
        final AgentCacheState agentCacheState = fileDistribution.getAgentCacheState();
        final Condition cacheStateCondition = new Condition();
        agentCacheState.addListener(new PropertyChangeListener() {
            public void propertyChange(PropertyChangeEvent ignored) {
                synchronized (cacheStateCondition) {
                    cacheStateCondition.notifyAll();
                }
            }
        });
        final MutableBoolean safeDist = new MutableBoolean(safe);
        ConsoleProperties consoleComponent = getConsoleComponent(ConsoleProperties.class);
        final File file = consoleComponent.getDistributionDirectory().getFile();
        if (listener != null) {
            listener.apply(new Informer<FileDistributionListener>() {
                @Override
                public void inform(FileDistributionListener listener) {
                    safeDist.setValue(listener.start(file, safe));
                }
            });
        }
        final FileDistributionHandler distributionHandler = fileDistribution.getHandler();
        // When cancel is called.. stop processing.
        int fileCount = 0;
        while (!cancel) {
            try {
                // distribute files in directory
                final FileDistributionHandler.Result result = distributionHandler.sendNextFile();
                fileCount++;
                if (result == null) {
                    break;
                }
                if (listener != null) {
                    listener.apply(new Informer<FileDistributionListener>() {
                        @Override
                        public void inform(FileDistributionListener listener) {
                            listener.distributed(result.getFileName());
                        }
                    });
                }

                if (safeDist.isTrue()) {
                    // The cache status is updated asynchronously by agent
                    // reports. If the listener is registered, this waits for up
                    // to five seconds for
                    // all agents to indicate that they are up to date.
                    checkSafetyWithCacheState(fileDistribution, cacheStateCondition, 1);
                }
            } catch (FileContents.FileContentsException e) {
                throw processException("Error while distribute files for " + getConsolePort());
            }
        }
        if (safeDist.isFalse()) {
            ThreadUtils.sleep(1000);
            checkSafetyWithCacheState(fileDistribution, cacheStateCondition, fileCount);
        }
    }
```

net/sf/grinder/grinder-core/3.9.1/grinder-core-3.9.1.jar!/net/grinder/console/communication/DistributionControlImplementation.class
``` java
  public Result sendNextFile() throws FileContents.FileContentsException {
    try {
      if (m_fileIndex < m_files.length) {
        if (m_fileIndex == 0) {
          // Clear any cache that has out of date cache parameters.
          // We currently we do nothing about cached copies of deleted files.
          final Address addressAgentsWithInvalidCaches = m_agents.getAddressOfOutOfDateAgents(0);

          m_distributionControl.clearFileCaches(addressAgentsWithInvalidCaches);
        }

        try {
          final int index = m_fileIndex;
          final File file = m_files[index];

          final Address addressAgentsWithoutFile = m_agents.getAddressOfOutOfDateAgents(new File(m_directory, file.getPath()).lastModified());
          // send one file to address of one agent
          m_distributionControl.sendFile(addressAgentsWithoutFile, new FileContents(m_directory, file));

          return new Result() {
              public int getProgressInCents() {
                return ((index + 1) * 100) / m_files.length;
              }

              public String getFileName() {
                return file.getPath();
              }
            };
        } finally {
          ++m_fileIndex;
        }
      } else {
        m_distributionControl.setHighWaterMark(
          m_agents.getAddressOfAllAgents(),
          m_cacheParameters.createHighWaterMark(m_latestFileTime));

        return null;
      }
    } catch (OutOfDateException e) {
      return null;
    }
  }
```
net/sf/grinder/grinder-core/3.9.1/grinder-core-3.9.1.jar!/net/grinder/console/communication/DistributionControlImplementation.class
``` java
/**
   * Send a file to the agents matching the given address.
   *
   * @param address
   *            The address of the agents.
   * @param fileContents The file contents.
   */
  public void sendFile(Address address, FileContents fileContents) {
    m_consoleCommunication.sendToAddressedAgents(address, new DistributeFileMessage(fileContents));
  }
```

##### agent

``` java
    /**
     * Run the Grinder agent process.
     *
     * @param grinderProperties {@link GrinderProperties} which contains grinder agent base configuration.
     * @throws GrinderException If an error occurs.
     */
    public void run(GrinderProperties grinderProperties) throws GrinderException {
        StartGrinderMessage startMessage = null;
        ConsoleCommunication consoleCommunication = null;
        m_fanOutStreamSender = new FanOutStreamSender(GrinderConstants.AGENT_FANOUT_STREAM_THREAD_COUNT);
        m_timer = new Timer(false);
        try {
            while (true) {
                m_logger.info(GrinderBuild.getName());
                ScriptLocation script = null;
                GrinderProperties properties;

                do {
                    properties = createAndMergeProperties(grinderProperties, startMessage != null ? startMessage.getProperties() : null);
                    properties.setProperty(GrinderProperties.CONSOLE_HOST, m_agentConfig.getControllerIP());
                    m_agentIdentity.setName(m_agentConfig.getAgentHostID());
                    final Connector connector = m_connectorFactory.create(properties);
                    // We only reconnect if the connection details have changed.
                    if (consoleCommunication != null && !consoleCommunication.getConnector().equals(connector)) {
                        shutdownConsoleCommunication(consoleCommunication);
                        consoleCommunication = null;
                        // Accept any startMessage from previous console - see
                        // bug 2092881.
                    }

                    if (consoleCommunication == null && connector != null) {
                        try {
                            consoleCommunication = new ConsoleCommunication(connector, grinderProperties.getProperty("grinder.user", "_default"));
                            consoleCommunication.start();
                            m_logger.info("Connect to console at {}", connector.getEndpointAsString());
                        } catch (CommunicationException e) {
                            if (m_proceedWithoutConsole) {
                                m_logger.warn("{}, proceeding without the console; set "
                                        + "grinder.useConsole=false to disable this warning.", e.getMessage());
                            } else {
                                m_logger.error(e.getMessage());
                                return;
                            }
                        }
                    }

                    if (consoleCommunication != null && startMessage == null) {
                        m_logger.info("Waiting for console signal");
                        m_consoleListener.waitForMessage();

                        if (m_consoleListener.received(ConsoleListener.START)) {
                            startMessage = m_consoleListener.getLastStartGrinderMessage();
                            continue; // Loop to handle new properties.
                        } else {
                            break; // Another message, check at end of outer while loop.
                        }
                    }

                    if (startMessage != null) {

                        final GrinderProperties messageProperties = startMessage.getProperties();
                        final Directory fileStoreDirectory = m_fileStore.getDirectory();

                        // Convert relative path to absolute path.
                        messageProperties.setAssociatedFile(fileStoreDirectory.getFile(messageProperties .getAssociatedFile()));

                        final File consoleScript = messageProperties.resolveRelativeFile(messageProperties.getFile(GrinderProperties.SCRIPT, GrinderProperties.DEFAULT_SCRIPT));

                        // We only fall back to the agent properties if the start message
                        // doesn't specify a script and there is no default script.
                        if (messageProperties.containsKey(GrinderProperties.SCRIPT) || consoleScript.canRead()) {
                            // The script directory may not be the file's direct parent.
                            script = new ScriptLocation(fileStoreDirectory, consoleScript);
                        }
                        m_agentIdentity.setNumber(startMessage.getAgentNumber());
                    } else {
                        m_agentIdentity.setNumber(-1);
                    }

                    if (script == null) {
                        final File scriptFile = properties.resolveRelativeFile(properties.getFile(GrinderProperties.SCRIPT, GrinderProperties.DEFAULT_SCRIPT));
                        script = new ScriptLocation(scriptFile);
                    }
                    m_logger.debug("The script location is {}", script.getFile().getAbsolutePath());
                    if (!script.getFile().canRead()) {
                        m_logger.error("The script file '{}' does not exist or is not readable.", script);
                        script = null;
                        break;
                    }
                } while (script == null);

                if (script != null) {
                    // Set up log directory.
                    if (!properties.containsKey(GrinderProperties.LOG_DIRECTORY)) {
                        properties.setFile(GrinderProperties.LOG_DIRECTORY, new File(m_agentConfig.getHome() .getLogDirectory(), properties.getProperty(GRINDER_PROP_TEST_ID, "default")));
                    }
                    File logFile = new File(properties.getFile(GrinderProperties.LOG_DIRECTORY, new File(".")), m_agentIdentity.getName() + "-" + m_agentIdentity.getNumber() + ".log");
                    m_logger.info("log file : {}", logFile);
                    AbstractLanguageHandler handler = Lang.getByFileName(script.getFile()).getHandler();
                    final WorkerFactory workerFactory;
                    Properties rebasedSystemProperty = rebaseSystemClassPath(System.getProperties(), m_agentConfig.getCurrentDirectory());

                    String jvmArguments = buildTestRunProperties(script, handler, rebasedSystemProperty, properties);

                    if (!properties.getBoolean("grinder.debug.singleprocess", false)) {
                        // Fix to provide empty system classpath to speed up
                        final WorkerProcessCommandLine workerCommandLine = new WorkerProcessCommandLine(properties, filterSystemClassPath(rebasedSystemProperty, handler, m_logger), jvmArguments, script.getDirectory());

                        m_logger.info("Worker process command line: {}", workerCommandLine);
                        FileUtils.writeStringToFile(logFile, workerCommandLine.toString() + "\n\n");
                        workerFactory = new ProcessWorkerFactory(workerCommandLine, m_agentIdentity, m_fanOutStreamSender, consoleCommunication != null, script, properties);
                    } else {
                        m_logger.info("DEBUG MODE. Spawning threads rather than processes");
                        m_logger.warn("grinder.jvm.arguments ({}) ignored in single process mode", jvmArguments);

                        workerFactory = new DebugThreadWorkerFactory(m_agentIdentity, m_fanOutStreamSender, consoleCommunication != null, script, properties);
                    }
                    m_logger.debug("Worker launcher is prepared.");
                    final WorkerLauncher workerLauncher = new WorkerLauncher(properties.getInt("grinder.processes", 1), workerFactory, m_eventSynchronisation, m_logger);
                    m_workerLauncherForShutdown = workerLauncher;
                    final boolean threadRampUp = properties.getBoolean("grinder.threadRampUp", false);
                    final int increment = properties.getInt("grinder.processIncrement", 0);
                    if (!threadRampUp) {
                        m_logger.debug("'Ramp Up' mode by {}.", increment);
                    }
                    if (!threadRampUp && increment > 0) {
                        final boolean moreProcessesToStart = workerLauncher.startSomeWorkers(properties.getInt("grinder.initialProcesses", increment));

                        if (moreProcessesToStart) {
                            final int incrementInterval = properties.getInt("grinder.processIncrementInterval", 60000);

                            final RampUpTimerTask rampUpTimerTask = new RampUpTimerTask(workerLauncher, increment);

                            m_timer.scheduleAtFixedRate(rampUpTimerTask, incrementInterval, incrementInterval);
                        }
                    } else {
                        m_logger.debug("start all workers");
                        workerLauncher.startAllWorkers();
                    }

                    // Wait for a termination event.
                    synchronized (m_eventSynchronisation) {
                        final long maximumShutdownTime = 5000;
                        long consoleSignalTime = -1;
                        while (!workerLauncher.allFinished()) {
                            m_logger.debug("Waiting until all workers are finished");
                            if (consoleSignalTime == -1 && m_consoleListener.checkForMessage(ConsoleListener.ANY ^ ConsoleListener.START)) {
                                m_logger.info("Don't start anymore by message from controller.");
                                workerLauncher.dontStartAnyMore();
                                consoleSignalTime = System.currentTimeMillis();
                            }
                            if (consoleSignalTime >= 0 && System.currentTimeMillis() - consoleSignalTime > maximumShutdownTime) {

                                m_logger.info("Terminating unresponsive processes by force");

                                // destroyAllWorkers() prevents further workers
                                // from starting.
                                workerLauncher.destroyAllWorkers();
                            }
                            m_eventSynchronisation.waitNoInterrruptException(maximumShutdownTime);
                        }
                        m_logger.info("All workers are finished");
                    }
                    m_logger.debug("Normal shutdown");
                    workerLauncher.shutdown();
                    break;
                }

                if (consoleCommunication == null) {
                    m_logger.debug("Console communication death");
                    break;
                } else {
                    // Ignore any pending start messages.
                    m_consoleListener.discardMessages(ConsoleListener.START);

                    if (!m_consoleListener.received(ConsoleListener.ANY)) {
                        // We've got here naturally, without a console signal.
                        m_logger.debug("Test is finished, wait for console signal");
                        m_consoleListener.waitForMessage();
                    }

                    if (m_consoleListener.received(ConsoleListener.START)) {
                        startMessage = m_consoleListener.getLastStartGrinderMessage();

                    } else if (m_consoleListener.received(ConsoleListener.STOP | ConsoleListener.SHUTDOWN)) {
                        m_logger.debug("Got shutdown message");
                        break;
                    } else {
                        m_logger.debug("Natural death");
                        // ConsoleListener.RESET or natural death.
                        startMessage = null;
                    }
                }
            }
        } catch (Exception e) {
            m_logger.error("Exception occurred in the agent message loop", e);
        } finally {
            if (m_timer != null) {
                m_timer.cancel();
                m_timer = null;
            }
            shutdownConsoleCommunication(consoleCommunication);
            if (m_fanOutStreamSender != null) {
                m_fanOutStreamSender.shutdown();
                m_fanOutStreamSender = null;
            }
            m_consoleListener.shutdown();
            m_logger.info("Test shuts down.");
        }
    }
```

``` java
    private final class ConsoleCommunication {
        private final ClientSender m_sender;
        private final Connector m_connector;
        private final TimerTask m_reportRunningTask;
        private final MessagePump m_messagePump;

        public ConsoleCommunication(Connector connector, String user) throws CommunicationException, FileStore.FileStoreException {

            final ClientReceiver receiver = ClientReceiver.connect(connector, new AgentAddress(m_agentIdentity));
            m_sender = ClientSender.connect(receiver);
            m_connector = connector;

            if (m_fileStore == null) {
                // Only create the file store if we connected.
                File base = m_agentConfig.getHome().getDirectory();
                File directory = new File(new File(base, "file-store"), user);
                // create FileStore
                m_fileStore = new FileStore(directory, m_logger);
            }

            m_sender.send(new AgentProcessReportMessage(ProcessReport.STATE_STARTED, m_fileStore .getCacheHighWaterMark()));

            final MessageDispatchSender fileStoreMessageDispatcher = new MessageDispatchSender();
            m_fileStore.registerMessageHandlers(fileStoreMessageDispatcher);

            final MessageDispatchSender messageDispatcher = new MessageDispatchSender();
            m_consoleListener.registerMessageHandlers(messageDispatcher);

            // Everything that the file store doesn't handle is tee'd to the
            // worker processes and our message handlers.
            fileStoreMessageDispatcher.addFallback(new TeeSender(messageDispatcher, new IgnoreShutdownSender(m_fanOutStreamSender)));

            m_messagePump = new MessagePump(receiver, fileStoreMessageDispatcher, 1);

            m_reportRunningTask = new TimerTask() {
                public void run() {
                    try {
                        m_sender.send(new AgentProcessReportMessage(ProcessReport.STATE_RUNNING, m_fileStore .getCacheHighWaterMark()));
                    } catch (CommunicationException e) {
                        cancel();
                        m_logger.error("Error while pumping up the AgentProcessReportMessage", e.getMessage());
                        m_logger.debug("The error detail is ", e);
                    }

                }
            };
        }

        public void start() {
            m_messagePump.start();
            m_timer.schedule(m_reportRunningTask, GrinderConstants.AGENT_HEARTBEAT_DELAY, GrinderConstants.AGENT_HEARTBEAT_INTERVAL);
        }

        public Connector getConnector() {
            return m_connector;
        }

        public void shutdown() {
            m_reportRunningTask.cancel();

            try {
                m_sender.send(new AgentProcessReportMessage(ProcessReport.STATE_FINISHED, m_fileStore .getCacheHighWaterMark()));
                m_logger.debug("Shut down message was sent");
            } catch (CommunicationException e) {
                NoOp.noOp();
            } finally {
                m_messagePump.shutdown();
            }
        }
    }
```


``` java
  /**
   * Registers message handlers with a dispatcher.
   *
   * @param messageDispatcher The dispatcher.
   */

  public void registerMessageHandlers(
    MessageDispatchRegistry messageDispatcher) {

    messageDispatcher.set(ClearCacheMessage.class, new AbstractHandler<ClearCacheMessage>() {
        public void handle(ClearCacheMessage message) throws CommunicationException {
          m_logger.info("Clearing file store");
          try {
            synchronized (m_incomingDirectory) {
                // clean
              m_incomingDirectory.deleteContents();
              m_incremental = false;
            }
          } catch (Directory.DirectoryException e) {
            m_logger.error(e.getMessage());
            throw new CommunicationException(e.getMessage(), e);
          }
        }
      });

    messageDispatcher.set(DistributeFileMessage.class, new AbstractHandler<DistributeFileMessage>() {
        public void handle(DistributeFileMessage message) throws CommunicationException {
          try {
            synchronized (m_incomingDirectory) {
              m_incomingDirectory.create();
              createReadmeFile();
              final FileContents fileContents = message.getFileContents();
              m_logger.info("Updating file store: {}", fileContents);
              // get distrbute file
              fileContents.create(m_incomingDirectory);
            }
          } catch (FileContents.FileContentsException e) {
            m_logger.error(e.getMessage());
            throw new CommunicationException(e.getMessage(), e);
          } catch (Directory.DirectoryException e) {
            m_logger.error(e.getMessage());
            throw new CommunicationException(e.getMessage(), e);
          }
        }
      });

    messageDispatcher.set(DistributionCacheCheckpointMessage.class, new AbstractHandler<DistributionCacheCheckpointMessage>() {
        public void handle(DistributionCacheCheckpointMessage message) {
          m_cacheHighWaterMark = message.getCacheHighWaterMark();
        }
      });
  }
```


#### 修改jar中class

``` shell
caohm@caohm-ThinkPad-E450:~/.m2/repository/net/sf/grinder/grinder-core/3.9.1$ unzip -l grinder-core-3.9.1.1.jar | grep "FileContents.class" | awk '{printf $4}' | xargs -I {} zip -d grinder-core-3.9.1.1.jar {}
caohm@caohm-ThinkPad-E450:~/.m2/repository/net/sf/grinder/grinder-core/3.9.1$ unzip -l grinder-core-3.9.1.1.jar | grep "FileDistributionHandlerImplementation.class" | awk '{printf $4}' | xargs -I {} zip -d grinder-core-3.9.1.1.jar {}
caohm@caohm-ThinkPad-E450:~/.m2/repository/net/sf/grinder/grinder-core/3.9.1$ unzip -l grinder-core-3.9.1.1.jar | grep "FileStore.class" | awk '{printf $4}' | xargs -I {} zip -d grinder-core-3.9.1.1.jar {}
caohm@caohm-ThinkPad-E450:~/.m2/repository/net/sf/grinder/grinder-core/3.9.1$ cp grinder-core-3.9.1.1.jar ~/github/caohm/ngrinder/lib/
```