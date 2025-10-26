# {{ .Title }}

{{- if .Content }}
{{ .Content }}
{{- end }}

{{- if .Data.Pages }}
{{- range .Data.Pages.GroupByDate "2006" }}
## {{ .Key }}
{{- range .Pages }}
- [{{ .Title }}]({{ .RelPermalink }}index.md)
{{- end }}
{{- end }}
{{- end }}
