Rationale for using Python / Django for Specify WebApp server
-------------------------------------------------------------
Specify is a very configurable software package. There is almost no
aspect of its operation that is not subject to various options or
re-purposing. The hierarchical form system is fully user definable
along multiple dimensions including user context, form types,
read-only vs. editing. Each of these dimensions can be determined by
numerous factors, the only true specifications of which being the
thick-client reference implementation. The data model includes
persistence of business data along side implementation data. The
business data itself exhibits the entire range of semantic rigor from
fields which are entirely free form to fields which are restricted to
controlled vocabularies. Indeed, even the level of rigor on a given
field is subject to configuration; e.g. the user can add a pick list
(controlled vocabulary) to what would otherwise be a free form
field. The semantic mapping of each field is itself determined by a
separate subsystem, termed schema localization, which also provides
for language localization. The UI representation of data values is, in
general, controlled by a number of interacting subsystems including
field formatters, data object formatters, and formatters specific to
data types such as dates. Again, all of these permutations are subject
to user configuration. And that's just the beginning.

Now consider the complexity and ill-definedness that results from the
interaction between those considerations and the constraints imposed
by the Web: concurrency, asynchronicity, statelessness, latency,
etc. The existing thick client comprises nearly half a million lines
of Java code for what is ultimately a glorified user interface to
MySQL. This is because the interplay of all these dimensions results
in a combinatorial explosion of the possible states that must be
handled. Simply introducing the further complications represented by
adding a web interface will result not in an arithmetic addition of
complexity, but another a combinatorial increase.

The only hope is to approach the problem in a modular fashion. The
solution must take the form of a tractable number of modules which can
themselves be composed to match the complexity of the problem
following the same combinatorial dynamic. The trick, of course, is
that the modules must function and combine in the right ways. This can
be achieved via two different strategies; it is the perennial top-down
vs. bottom-up dichotomy.

The top-down approach would mean designing the entire system, all of
the new modules and their interactions, upfront. The advantage is that
once that is accomplished, the coding itself is a straightforward
implementation of the resulting specification. The downside is that
nothing can be produced until the entire system has been
analyzed. Otherwise, you might end up implementing modules that don't
combine properly with those yet to be designed.

Working from the bottom-up entails choosing some portion of the system
you want the most and implementing it in the most straightforward way
possible. Further functionality is added iteratively. Modularity is
maintained in an ongoing process of refactoring and refining. The
advantage is that functionality is delivered rapidly and is flexible
to changing requirements. The downside is that code may be revisited
numerous times, and it becomes more difficult to parcel out the coding
since the design work is intermingled.

The upshot is, top-down would argue for the use of Java or other
static-ish language where design decisions are strongly
enforced. Coding would be directed toward predetermined
specifications, the language providing all kinds of static guarantees
about those specifications. Working bottom-up is favored by more
dynamic languages. Because the design is being worked out in parallel
with the actual code, strong static constraints just represent extra
work that has to be backed out when requirements or working
assumptions change.

For the Specify webapp the bottom-up, dynamic paradigm is more
appropriate. First of all, Specify itself is more "dynamic" than
"static". As described above, the application in many places makes use
of either weak or optional guarantees that are not natural to "static"
methodology. Secondly, our available resources mirror the bottom-up,
dynamic paradigm. We are several small separate groups collaborating
on a voluntary basis with no top-down organization to determine the
division of labor. We have no separate group of personnel that
specialize in design and architecture, nor do we have a time horizon
that is compatible with putting development on hold while broad based
design is ongoing.

Given this reasoning, there are numerous "dynamic" paradigm frameworks
and ecosystems to choose from. On the browser side, there is no choice
but Javascript. Server side, we have an existing prototype solution
based on Python with Django. It works and can be straightforwardly
extended to finish the job. Beyond this fact, Django and the Python
ecosystem are a good choice. Both are under active development by both
core developers and contributors. They have a proven track record and
are widely used in the field of web applications. I personally have
used Django for the better part of a decade since version 0.96; it is
now at version 1.4. The documentation is second to none, and there are
active mailing lists for both developers and users, as well as an IRC
channel. There are 42k conversations on Stack Overflow tagged with
"django". For comparison, there are 14k for "JSP", 13k for "Hibernate"
and 12k for "JSF". More than 4000 websites have been submitted to the
DjangoSites.org showcase, including science.nasa.gov and BioGPS.org, a
gene annotation portal.

In sum, Django / Python represents a proven technology that is well
suited to the problem space inhabited by a Specify web application
given the available resources. That is why I chose it for rapid
development of the prototype Specify WebApp back end, and there is no
reason that prototype should not be fleshed out into a shippable
solution.
