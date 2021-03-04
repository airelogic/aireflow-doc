Most text fields in the management UI can contain either XPath expressions, or a combination of literal text with liquid expressions.

## XPath
For a text field to contain an XPath expression, it must be in expression mode, this can be done by setting the toggle to On, as shown here. The toggle will display a preview of your XPath expression by evalutating it against the example input provided at the top of the workflow definition.

![expression]({{site.baseurl}}/assets/img/expression-toggle.png)

## Liquid

When a text field is in literal mode, it can contain liquid expressions. To access the context, expressions should be in the form `{{ context.your-value }}` where `context` replaces the base element of your input xml, and `your-value` is a dot sepearated path to your desired value.

Liquid expressions can also access the workflow instance itself, so you could for instance access the subject ID using something like `{{ workflow.SubjectId }}`.