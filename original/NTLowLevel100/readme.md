# Original posting

Colin Wilson
unread,
Oct 27, 2004, 1:09:35 PM
to
Ricardo Carmo wrote:
> Debug a service application
>
> Is it possible?
> How?
>
> Thanks.
> Ricardo Carmo

I use two approaches...

1. The simplest way is to compile the service, start it with the
service control manager, then use 'Run/Attach to process' to make
Delphi attach itself to the running service. You'll need to click the
'Show System Processes' checkbox in the 'Attach to Process' dialog in
Delphi.

2. Run your service as a regular application. You can then debug it
like you would debug any program. To facilitate doing this I've
published a free TDebugServiceApplication object - available in the 'NT
Low Level Utilities' package on my website. You use it like this...

In your services .DPR file - where it used to look like this...

program TestService;

uses
SvcMgr,
TestServiceImpl in 'TestServiceImpl.pas' {Service1: TService};

{$R *.RES}

begin
Application.Initialize;
Application.CreateForm(TService1, Service1);
Application.Run;
end.

Make it look like this...

program TestService;

uses
SvcMgr,
unitDebugService,
TestServiceImpl in 'TestServiceImpl.pas' {Service1: TService};

{$R *.RES}

begin
if (paramCount > 0) and (SameText (ParamStr (1), '-DEBUG')) then
begin
FreeAndNil (Application);
Application := TDebugServiceApplication.Create(nil);
end;
Application.Initialize;
Application.CreateForm(TService1, Service1);
Application.Run;
end.

Now, when you run your service with a '-debug' parameter it will run as
an application. I do this with all my services, so it's all well
tested. Give it a try - it works!


--
Colin - using XanaNews HTTP Transport
e-mail :co...@wilsonc.demon.co.uk