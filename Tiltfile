SOURCE_IMAGE = os.getenv("SOURCE_IMAGE", default='ghcr.io/halkyonio/spring-tap-petclinic-tap-demo')
LOCAL_PATH = os.getenv("LOCAL_PATH", default='.')
NAMESPACE = os.getenv("NAMESPACE", default='tap-demo')

update_settings (k8s_upsert_timeout_secs = 90) 

allow_k8s_contexts('kubernetes-admin@kubernetes')

k8s_custom_deploy(
    'spring-tap-petclinic',
    apply_cmd="tanzu apps workload apply -f config/workload.yaml --live-update" +
               " --local-path " + LOCAL_PATH +
               " --source-image " + SOURCE_IMAGE +
               " --namespace " + NAMESPACE +
               " --yes >/dev/null" +
               " && kubectl get workload spring-tap-petclinic --namespace " + NAMESPACE + " -o yaml",
    delete_cmd="tanzu apps workload delete -f config/workload.yaml --namespace " + NAMESPACE + " --yes",
    deps=['pom.xml', './target/classes', './target/classes/templates'],
    container_selector='workload',
    live_update=[
      sync('./target/classes','/workspace/BOOT-INF/classes'),
      sync('./target/classes/templates','/workspace/BOOT-INF/classes/templates')
    ]
)

k8s_resource('spring-tap-petclinic', port_forwards=["8080:8080"],
            extra_pod_selectors=[{'serving.knative.dev/service': 'spring-tap-petclinic'}])
