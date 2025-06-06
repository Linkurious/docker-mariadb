
# version_settings() enforces a minimum Tilt version
# https://docs.tilt.dev/api.html#api.version_settings
version_settings(constraint='>=0.30.8')

load('ext://kubectl_build', 'kubectl_build')
load('ext://helm_resource', 'helm_resource', 'helm_repo')

ctx = k8s_context()
if ctx.endswith('k8s-preprod'):
  allow_k8s_contexts(ctx)

if not k8s_namespace().endswith("dev"):
  fail("You are not targeting a dev namespace")
builder = "builder-" + k8s_namespace()

mariadb_workload_name = 'mariadbv5'
mariadb_release_name = ctx.removesuffix('@k8s-preprod') + '-tilt-mariadbv5'

deps=['charts/mariadb']
extra_values = ['--render-subchart-notes', '--values=charts/mariadb/values.preprod.yaml', '--values=charts/mariadb/values.preview.yaml', '--set=networkPolicies.allowAllNamespaceIngress=true']

internal_values_filename = 'values.internal.yaml'
if os.path.exists(internal_values_filename):
  extra_values = ['--values='+internal_values_filename]
  deps += [internal_values_filename]
helm_resource(
  name=mariadb_workload_name,
  release_name=mariadb_release_name,
  chart='charts/mariadb',
  deps=deps,
  flags=['--render-subchart-notes']
  )
k8s_resource(workload=mariadb_workload_name,
  links=[
      mariadb_release_name + '.mariadb-dev.k8s.preprod.linkurious.net',
  ]
)
