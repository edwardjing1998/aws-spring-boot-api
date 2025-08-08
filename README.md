node {
  checkout scm
  def branch = env.BRANCH_NAME

  def result = gfsCloudPipelineV2(
        releaseOptions: '',
        emailTo: 'harishchander.baswapuram@fiserv.com',
        deployToQA: false,
        waitTimeForRegressions: '240',
        buildContainerImage: true,
        jdkVersion: 'jdk21',
        serviceType: 'springboot',
        dynamicScanId: ["9527dc24-ccc6-4e30-85dd-62c95d9c8b1b"]
      )
  // release version can be SNAPSHOT version if the pipeline was triggered  by scan schedule
  if(branch ==~ /release.*/ && !(result.releaseVersion ==~ /.*-SNAPSHOT/)) {
    print 'updating version in product helm yaml'
    gfsProductServiceVersionUpdate(
        product: 'gfs-fos-client-sysprin-1',
        branch: 'release/TRACE-Client-microservices-Java',
        service: 'gfs-fos-client-sysprin-microservices',
        version: result.releaseVersion,
        repoUrl: 'https://gitlab.onefiserv.net/issuers/fos-modernization/plastic/rapid/RAPID-Client-microservices-Java.git'
      )
  }
}
