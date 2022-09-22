% GET_CHANNEL(x,channel)
%
% returns abs,angle,real,imag depending on channel flag
%
%   channel:
%       1,'abs'
%       2,'angle'
%       3,'real'
%       4,'imag'
function xc = get_channel(x,channel)

switch channel
    case {1,'abs'}
        xc = abs(x);
    case {2,'angle'}
        xc = angle(x);
    case {3,'real'}
        xc = real(x);
    case {4,'imag'}
        xc = imag(x);
end;
